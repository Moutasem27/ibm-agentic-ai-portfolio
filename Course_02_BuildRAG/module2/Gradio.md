# Gradio
* Open-source python library
* Enables the creation of customizable web-based user interfaces
* Designed for ease of use
* Ideal for machine learning models and computational tools
## How dose it work?
1. Write Python code to define functions for app
2. Use Gradio to create an interface by specifying inputs and outputs then configure it to define user interaction
3. Launch Gradio server using launch method by starting a local server on machine and create a web interface for your app
4. Final step is to access web interface through local or public URL provided by Gradio.

## Code block example
pip install gradio \
import gradio as gr \

def process_text(text): \
  return f"You entered: '{text}'" \

demo = gr.Interface( fn = process_text, inputs = gr.Textbox(label = "Enter some text"), outputs = gr.Textbox(label = "Output")
\
demo.launch()

## you can have multiple inputs instead of one by putting them into a list
inputs = [gr.Number(label = "Enter a number"), gr.Textbox(label = "Enter some text"],

## To upload/drop files
inputs = gr.File(file_count = "multiple", type = "filepath", label = "Upload or Drag files here")
