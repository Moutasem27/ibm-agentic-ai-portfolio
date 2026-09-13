# Cheat Sheet: Foundations of Multimodal AI — Comprehensive Code & Implementation Reference
*A Technical Summary of Image Processing, Model Setup, and Multimodal Queries*

---

## 1. Image Processing & Encoding
Essential helper functions for preparing and encoding local images for multimodal model inputs:

```python
import base64
from PIL import Image
from io import BytesIO

def encode_image(image_path):
    """Convert image to base64 for model input."""
    with open(image_path, "rb") as image_file:
        encoded_string = base64.b64encode(image_file.read()).decode('utf-8')
    return encoded_string

def process_image(image_path, target_size=(224, 224)):
    """Process image for model input."""
    image = Image.open(image_path)
    image = image.resize(target_size)
    return image

# Example usage
image_path = "example.jpg"
encoded_image = encode_image(image_path)
processed_image = process_image(image_path)
