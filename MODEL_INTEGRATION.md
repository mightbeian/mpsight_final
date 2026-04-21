# Model Integration Guide

This guide explains how to integrate your trained YOLOv8 classification model into the MPSight app.

## Quick Start

### If you have a `.tflite` file ready:

1. **Replace the placeholder:**
   ```bash
   # Copy your trained model to the assets folder
   copy your_trained_model.tflite assets\models\mpox_classifier.tflite
   ```

2. **Verify the file:**
   ```bash
   # Check the file exists and has reasonable size
   dir assets\models\mpox_classifier.tflite
   ```
   - File size should be > 1MB (typically 5-20MB)
   - If it's only a few bytes, it's still the placeholder

3. **Run the app:**
   ```bash
   flutter run
   ```

### If you have a PyTorch `.pt` or `.pth` file:

1. **Update the conversion script:**
   
   Edit `convert_to_tflite.py` line 28:
   ```python
   model_path = r"path\to\your\trained\model\best.pt"
   ```

2. **Run the conversion:**
   ```bash
   python convert_to_tflite.py
   ```
   
   This will:
   - Install required dependencies (ultralytics, tensorflow, etc.)
   - Load your PyTorch model
   - Export to TFLite format
   - Copy to `assets/models/mpox_classifier.tflite`
   - Create a `labels.txt` file

3. **Run the app:**
   ```bash
   flutter run
   ```

## Model Requirements

### Input Specifications
- **Shape:** `[1, 640, 640, 3]`
- **Type:** Float32
- **Normalization:** Values between 0.0 and 1.0 (divide by 255)
- **Color Format:** RGB
- **Size:** 640x640 pixels (will be resized automatically)

### Output Specifications
- **Shape:** `[1, 6]`
- **Type:** Float32
- **Values:** Confidence scores for each class (will be multiplied by 100 for percentage)

### Class Labels (MSLD v2.0)
The model must output predictions in this exact order:
1. Chickenpox
2. Cowpox
3. Healthy
4. HFMD (Hand, Foot, and Mouth Disease)
5. Measles
6. Monkeypox

**Important:** The class order must match your training dataset. If your model has different class ordering, update the `_labels` list in `lib/providers/detection_provider.dart`.

## Verification Steps

### 1. Check Model File
```bash
# Windows PowerShell
Get-Item assets\models\mpox_classifier.tflite | Select-Object Name, Length, LastWriteTime

# Expected output:
# Name                   Length LastWriteTime
# ----                   ------ -------------
# mpox_classifier.tflite 5234567 11/19/2025 ...
```

### 2. Run the App in Debug Mode
```bash
flutter run
```

Look for these console messages:

✅ **Success:**
```
📊 Model Input Shape: [1, 640, 640, 3]
📊 Model Output Shape: [1, 6]
📊 Model Classes: [Chickenpox, Cowpox, Healthy, HFMD, Measles, Monkeypox]
✅ Model loaded successfully and ready for detection
```

❌ **Failure:**
```
❌ Error loading model: <error details>
⚠️ Running with mock data for testing
💡 Tip: Ensure mpox_classifier.tflite is properly placed in assets/models/
```

### 3. Test Detection
1. Launch the app
2. Tap "Capture Photo" or "Gallery"
3. Take/select an image
4. Check the debug console for detection logs:
   ```
   🔍 detectSkinCondition called
   📸 Decoding image...
   🖼️ Resizing image to 640x640...
   🔄 Converting to Float32...
   📊 Preparing output tensor...
   🤖 Running inference...
   ✅ Processing results...
   🎯 Primary condition: Monkeypox (85.3%)
   ```

## Troubleshooting

### Problem: Model not loading
**Symptoms:**
- Console shows "Error loading model"
- App runs with mock data

**Solutions:**
1. Verify file exists: `Test-Path assets\models\mpox_classifier.tflite`
2. Check file size (should be > 1MB)
3. Ensure file isn't corrupted
4. Rebuild the app: `flutter clean && flutter pub get && flutter run`

### Problem: Wrong predictions
**Symptoms:**
- Predictions don't make sense
- All predictions are similar confidence

**Solutions:**
1. Verify class order matches training
2. Check model input preprocessing (should be RGB, normalized)
3. Ensure model was trained with 640x640 images
4. Verify the model file is the correct version

### Problem: App crashes on detection
**Symptoms:**
- App works until you try to detect
- Console shows tensor shape mismatch

**Solutions:**
1. Verify model output shape is `[1, 6]`
2. Check if model expects different input size
3. Update `detection_provider.dart` if model specs differ

### Problem: Slow inference
**Symptoms:**
- Detection takes > 2 seconds
- App feels sluggish

**Solutions:**
1. Ensure NNAPI is enabled (should be automatic on Android)
2. Consider using quantized model (int8)
3. Reduce image size if accuracy isn't critical
4. Check device performance

## Model Update Workflow

When you have a new/improved model:

1. **Backup current model (optional):**
   ```bash
   copy assets\models\mpox_classifier.tflite assets\models\mpox_classifier_backup.tflite
   ```

2. **Replace with new model:**
   ```bash
   copy new_model.tflite assets\models\mpox_classifier.tflite
   ```

3. **Clean and rebuild:**
   ```bash
   flutter clean
   flutter pub get
   flutter run
   ```

4. **Test thoroughly:**
   - Test with various image types
   - Verify predictions make sense
   - Check inference speed
   - Test on physical device

## Advanced Configuration

### Adjusting Inference Parameters

Edit `lib/providers/detection_provider.dart`:

```dart
// Change thread count (default: 4)
final options = InterpreterOptions()
  ..threads = 2  // Lower for older devices, higher for newer

// Disable NNAPI if causing issues
final options = InterpreterOptions()
  ..useNnApiForAndroid = false

// Add delegates for GPU acceleration
final options = InterpreterOptions()
  ..addDelegate(GpuDelegate())
```

### Updating Class Labels

If your model has different classes, update in `detection_provider.dart`:

```dart
final List<String> _labels = [
  'YourClass1',
  'YourClass2',
  'YourClass3',
  // ... etc
];
```

### Changing Input Size

If your model uses a different input size (e.g., 224x224):

1. Update in `detection_provider.dart`:
   ```dart
   // Change all occurrences of 640 to your size
   img.Image resizedImage = img.copyResize(
     image,
     width: 224,  // Your size
     height: 224, // Your size
     interpolation: img.Interpolation.linear,
   );
   ```

2. Update tensor creation accordingly

## Getting Help

If you encounter issues:

1. **Check console logs** - Most issues show detailed error messages
2. **Verify model specs** - Ensure input/output shapes match
3. **Test with mock mode** - Confirm app works without real model
4. **Check Flutter doctor** - Run `flutter doctor` for environment issues

## Model Performance Tips

### For Better Accuracy:
- Use high-resolution training images (640x640)
- Ensure diverse training dataset
- Use appropriate data augmentation
- Train for sufficient epochs
- Validate on held-out test set

### For Faster Inference:
- Use quantized model (int8 instead of float32)
- Enable hardware acceleration (NNAPI, GPU)
- Consider smaller model (YOLOv8n instead of YOLOv8m)
- Optimize TFLite with optimization flags

### For Smaller Model Size:
- Use quantization (can reduce size by 4x)
- Prune unnecessary layers
- Use YOLOv8n (nano) variant
- Apply post-training optimization

## File Locations Summary

```
mpsight-app/
├── assets/models/
│   ├── mpox_classifier.tflite    ← Your trained model goes here
│   ├── labels.txt                ← Optional: class labels reference
│   └── README.md                 ← Model specifications
├── lib/providers/
│   └── detection_provider.dart   ← Model loading and inference code
├── convert_to_tflite.py         ← PyTorch → TFLite conversion script
└── MODEL_INTEGRATION.md          ← This file
```

## Next Steps

After successful integration:

1. ✅ Test on multiple device types (low-end, mid-range, high-end)
2. ✅ Validate predictions against ground truth
3. ✅ Measure inference time and optimize if needed
4. ✅ Test with edge cases (blurry images, poor lighting, etc.)
5. ✅ Consider A/B testing with previous model version
6. ✅ Monitor crash reports and user feedback
7. ✅ Document model version and performance metrics

Good luck with your model integration! 🚀
