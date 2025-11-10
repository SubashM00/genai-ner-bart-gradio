Skip to content
Navigation Menu
Sunilkumar074
genai-ner-bart-gradio

Type / to search
Code
Pull requests
Actions
Projects
Security
Insights
Owner avatar
genai-ner-bart-gradio
Public
forked from obedotto/genai-ner-bart-gradio
Sunilkumar074/genai-ner-bart-gradio
Go to file
t
This branch is 3 commits ahead of obedotto/genai-ner-bart-gradio:main.
Name		
Sunilkumar074
Sunilkumar074
Modify example name and update output image
ad6d3c9
 · 
last week
README.md
Modify example name and update output image
last week
Repository files navigation
README
NAME: SUBASH M
REGNO: 212223049129
Development of a Named Entity Recognition (NER) Prototype Using a Fine-Tuned BART Model and Gradio Framework
AIM:
To design and develop a prototype application for Named Entity Recognition (NER) by leveraging a fine-tuned BART model and deploying the application using the Gradio framework for user interaction and evaluation.

PROBLEM STATEMENT:
The challenge is to build an NER system capable of identifying named entities (e.g., people, organizations, locations) in text, using a pre-trained BART model fine-tuned for this task. The system should be interactive, allowing users to input text and see the recognized entities in real-time.

DESIGN STEPS:
STEP 1: Fine-tune the BART model
Start by fine-tuning the BART model for NER tasks. This involves training the model on a labeled NER dataset with text data that contains named entities (e.g., people, places, organizations).

STEP 2: Create an API for NER model inference
Develop an API endpoint that takes input text and returns the recognized entities using the fine-tuned BART model.

STEP 3: Integrate the API with Gradio
Build a Gradio interface that takes user input, passes it to the NER model via the API, and displays the results as highlighted text with identified entities.

PROGRAM:
import os
import json
import requests
import gradio as gr
from dotenv import load_dotenv, find_dotenv

_ = load_dotenv(find_dotenv())
hf_api_key = os.environ['HF_API_KEY']
API_URL = os.environ['HF_API_NER_BASE']

def get_completion(inputs, parameters=None, ENDPOINT_URL=API_URL):
    headers = {
        "Authorization": f"Bearer {hf_api_key}",
        "Content-Type": "application/json"
    }
    data = {"inputs": inputs}
    if parameters:
        data.update({"parameters": parameters})

    response = requests.post(ENDPOINT_URL, headers=headers, data=json.dumps(data))
    text = response.content.decode("utf-8").strip()

    # Handle extra data safely
    try:
        # Try parsing as normal JSON
        return json.loads(text)
    except json.JSONDecodeError:
        # If response contains multiple JSON objects, take the first valid one
        parts = text.split("\n")
        for part in parts:
            try:
                return json.loads(part)
            except Exception:
                continue
        raise ValueError(f"Invalid JSON returned from model: {text}")

def merge_tokens(tokens):
    merged_tokens = []
    for token in tokens:
        if merged_tokens and token['entity'].startswith('I-') and merged_tokens[-1]['entity'].endswith(token['entity'][2:]):
            last = merged_tokens[-1]
            last['word'] += token['word'].replace('##', '')
            last['end'] = token['end']
            last['score'] = (last['score'] + token['score']) / 2
        else:
            merged_tokens.append(token)
    return merged_tokens

def ner(input_text):
    output = get_completion(input_text)
    if not isinstance(output, list):
        raise ValueError(f"Unexpected model output: {output}")
    merged_tokens = merge_tokens(output)
    return {"text": input_text, "entities": merged_tokens}

gr.close_all()
demo = gr.Interface(
    fn=ner,
    inputs=[gr.Textbox(label="Text to find entities", lines=2)],
    outputs=[gr.HighlightedText(label="Text with entities")],
    title="NER with dslim/bert-base-NER",
    description="Find named entities using the dslim/bert-base-NER model via Hugging Face Inference API.",
    allow_flagging="never",
    examples=[
        "My name is SK, I work at DeepLearningAI and live in Chennai.",
        "Rohit lives in Chennai and works at HuggingFace."
    ]
)

demo.launch(share=True, server_port=int(os.environ.get("PORT3", 7860)))
OUTPUT:
Screenshot 2025-10-31 115340
RESULT:
Thus, developed an NER prototype application with user interaction and evaluation features, using a fine-tuned BART model deployed through the Gradio framework.

About
No description, website, or topics provided.
Resources
 Readme
 Activity
Stars
 0 stars
Watchers
 0 watching
Forks
 0 forks
Report repository
Releases
No releases published
Packages
No packages published
Footer
© 2025 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Community
Docs
Contact
Manage cookies
Do not share my personal information
Editing genai-ner-bart-gradio/README.md at main · Sunilkumar074/genai-ner-bart-gradio
