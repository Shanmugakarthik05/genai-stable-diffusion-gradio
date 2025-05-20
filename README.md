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
```
import os
import io
import IPython.display
from PIL import Image
import base64 
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']

# Helper function
import requests, json

#Text-to-image endpoint
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_TTI_BASE']):
    headers = {
      "Authorization": f"Bearer {hf_api_key}",
      "Content-Type": "application/json"
    }   
    data = { "inputs": inputs }
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST",
                                ENDPOINT_URL,
                                headers=headers,
                                data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))

prompt = "a dog in a park"

result = get_completion(prompt)
IPython.display.HTML(f'<img src="data:image/png;base64,{result}" />')

import gradio as gr 

#A helper function to convert the PIL image to base64
#so you can send it to the API
def base64_to_pil(img_base64):
    base64_decoded = base64.b64decode(img_base64)
    byte_stream = io.BytesIO(base64_decoded)
    pil_image = Image.open(byte_stream)
    return pil_image

def generate(prompt):
    output = get_completion(prompt)
    result_image = base64_to_pil(output)
    return result_image

gr.close_all()
demo = gr.Interface(fn=generate,
                    inputs=[gr.Textbox(label="Your prompt")],
                    outputs=[gr.Image(label="Result")],
                    title="Image Generation with Stable Diffusion",
                    description="Generate any image with Stable Diffusion",
                    allow_flagging="never",
                    examples=["the spirit of a tamagotchi wandering in the city of Vienna","a mecha robot in a favela"])

demo.launch(share=True, server_port=int(os.environ['PORT1']))

demo.close()
```
### OUTPUT:
![alt text](<Screenshot 2025-05-20 224501.png>)
### RESULT:
Thus, The designing and deploying of a prototype application for image generation by utilizing the Stable Diffusion model, integrated with the Gradio UI framework is executed successfully.