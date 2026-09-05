## Prototype Development for Image Generation Using the Stable Diffusion Model and Gradio Framework
#### Name: Gayathri K
#### Register Number: 212223230061
### AIM:
To design and deploy a prototype application for image generation utilizing the Stable Diffusion model, integrated with the Gradio UI framework for interactive user engagement and evaluation.

### PROBLEM STATEMENT:
Traditional image generation models often lack user-friendly interfaces, requiring complex local setups or API knowledge to generate visuals from natural language prompts. The goal is to build an accessible web interface that takes text prompts from users, sends them to a hosted Stable Diffusion endpoint via Hugging Face API, decodes the resulting base64 payload into an image, and renders the result dynamically using Gradio.
### DESIGN STEPS:

#### STEP 1:
Load environment variables to retrieve the Hugging Face API key and target API endpoint URL. Define a POST request helper function (get_completion) to send text prompts as JSON and handle authorization.

#### STEP 2:
Create image conversion logic (base64_to_pil) using base64 decoding and io.BytesIO to convert the base64-encoded response payload into a displayable PIL image.

#### STEP 3:
Construct a web interface using gradio.Interface, binding the generate pipeline function to a text input box and image output display, and launch the web server on a designated environment port with a public share link.


### PROGRAM:
```py
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

prompt = "A beautiful house surrounded by green trees"

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
```
### OUTPUT:
<img width="702" height="607" alt="image" src="https://github.com/user-attachments/assets/c781bd96-01d5-4105-84c2-0a50f0d708bc" />
<img width="798" height="482" alt="image" src="https://github.com/user-attachments/assets/bc3eeb0d-51e1-4fe6-97db-83a654d8de0a" />
<img width="813" height="446" alt="image" src="https://github.com/user-attachments/assets/f5b5793b-98aa-4b76-8942-d731f593e02f" />

### RESULT:
The prototype application for Stable Diffusion image generation using the Gradio framework was successfully developed.
