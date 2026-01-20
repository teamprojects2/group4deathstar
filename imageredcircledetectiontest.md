```python
import cv2
```


```python
import numpy as np
```


```python
import os
```


```python
import shutil
```


```python
import zipfile
```


```python
BASE_FOLDER = os.getcwd()          # This should be teamprojects2
SOURCE_FOLDER = os.path.join(BASE_FOLDER, 'images')
DETECTED_FOLDER = os.path.join(BASE_FOLDER, 'detected')
```


```python
# Create detected folder if it doesn't exist
os.makedirs(DETECTED_FOLDER, exist_ok=True)
```


```python
# Step 2: Function to detect red circles
def has_red_circle(image_path):
    # Read image
    img = cv2.imread(image_path)
    if img is None:
        print(f"Warning: Cannot read {image_path}")
        return False

    # Convert to HSV for easier red color detection
    hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

    # Red hue wraps around, so we need two ranges
    lower_red1 = np.array([0, 100, 100])
    upper_red1 = np.array([10, 255, 255])
    lower_red2 = np.array([160, 100, 100])
    upper_red2 = np.array([179, 255, 255])

    # Mask for red
    mask1 = cv2.inRange(hsv, lower_red1, upper_red1)
    mask2 = cv2.inRange(hsv, lower_red2, upper_red2)
    mask = mask1 | mask2

    # Blur to reduce noise
    mask = cv2.GaussianBlur(mask, (9, 9), 2)

    # Detect circles
    circles = cv2.HoughCircles(mask, cv2.HOUGH_GRADIENT, dp=1.2, minDist=30,
                               param1=50, param2=15, minRadius=5, maxRadius=100)

    return circles is not None
```


```python
# Step 3: Process all images in 'images/' and move to 'detected/' if they have red circles
hit_images = []

for filename in os.listdir(SOURCE_FOLDER):
    path = os.path.join(SOURCE_FOLDER, filename)
    
    # Only process image files
    if not filename.lower().endswith(('.png', '.jpg', '.jpeg')):
        continue

    if has_red_circle(path):
        # Copy image to detected folder
        dest_path = os.path.join(DETECTED_FOLDER, filename)
        shutil.copy2(path, dest_path)
        hit_images.append(filename)  # Save just the filename for reporting

# Step 4: Summary
if hit_images:
    print(f"Detected red circles in {len(hit_images)} images.")
    print("Images moved/copied to 'detected' folder:")
    for img in hit_images:
        print(f" - {img}")
else:
    print("No red circles detected — 'detected' folder is empty.")
```

    Detected red circles in 3 images.
    Images moved/copied to 'detected' folder:
     - image13.png
     - image35.png
     - image9.png
    


```python

```
