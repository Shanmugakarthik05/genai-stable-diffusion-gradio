## Prototype Development for Image Generation Using the Stable Diffusion Model and Gradio Framework

### AIM:
To design and deploy a prototype application for image generation utilizing the Stable Diffusion model, integrated with the Gradio UI framework for interactive user engagement and evaluation.

### PROBLEM STATEMENT:
To develop an interactive application that allows users to generate custom images from text prompts using a state-of-the-art text-to-image model. The system should be user-friendly, enabling efficient testing and evaluation of the model's capabilities through a Gradio-based interface.
### DESIGN STEPS:

#### STEP 1: Understand the API and Framework Requirements 
- Identify the Stable Diffusion model's inference endpoint.  
- Set up authentication using Hugging Face API keys and configure environment variables.
  
#### STEP 2: Develop Backend Logic 
- Implement API request logic to send prompts and receive image outputs.  
- Convert API responses (base64-encoded images) into displayable image formats.  

#### STEP 3: Design Gradio Interface 
- Build an interactive Gradio interface to accept prompts and display images.  
- Add labels, descriptions, and examples for a user-friendly experience.

### PROGRAM:
```py
import os
import io
from PIL import Image
import base64
from dotenv import load_dotenv, find_dotenv
import requests
import json
import gradio as gr

# Load API key from environment variables
_ = load_dotenv(find_dotenv())
hf_api_key = os.environ['HF_API_KEY']

# Helper function to interact with Hugging Face API
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_TTI_BASE']):
    headers = {
        "Authorization": f"Bearer {hf_api_key}",
        "Content-Type": "application/json"
    }
    data = {"inputs": inputs}
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST", ENDPOINT_URL, headers=headers, data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))

# Convert base64-encoded string to PIL Image
def base64_to_pil(img_base64):
    base64_decoded = base64.b64decode(img_base64)
    byte_stream = io.BytesIO(base64_decoded)
    pil_image = Image.open(byte_stream)
    return pil_image

# Gradio generation function
def generate(prompt):
    output = get_completion(prompt)  # Get image data from API
    result_image = base64_to_pil(output["data"][0]["base64"])  # Convert to PIL Image
    return result_image

# Gradio interface
gr.close_all()
demo = gr.Interface(
    fn=generate,
    inputs=[gr.Textbox(label="Your prompt")],
    outputs=[gr.Image(label="Result")],
    title="Image Generation with Stable Diffusion",
    description="Generate any image with Stable Diffusion",
    allow_flagging="never",
    examples=["the spirit of a tamagotchi wandering in the city of Vienna", "a mecha robot in a favela"]
)

# Launch Gradio app
demo.launch()

```
### OUTPUT:
![alt text](<Screenshot 2025-05-13 231814.png>)
### RESULT:
Successfully designed and deployed a prototype application for image generation using the Stable Diffusion model, demonstrating interactive user engagement and the ability to generate high-quality images from text prompts.