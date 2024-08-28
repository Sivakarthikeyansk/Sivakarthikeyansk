multilingual-ai-bot/
│
├── main.py                   # Main entry point
├── stt.py                    # Speech-to-Text logic
├── nlu.py                    # Natural Language Understanding logic
├── dms.py                    # Dialogue Management System
├── nlg.py                    # Natural Language Generation
├── tts.py                    # Text-to-Speech logic
└── requirements.txt          # Python dependencies
from stt import speech_to_text
from nlu import analyze_intent
from dms import DialogueManager
from nlg import generate_response
from tts import text_to_speech

def main():
    print("Listening for user's query...")

    # Step 1: Capture user input (Speech to Text)
    user_input = speech_to_text()
    
    # Step 2: Analyze intent and context (NLU)
    intent, context = analyze_intent(user_input)

    # Step 3: Manage dialogue flow based on character and context
    dm = DialogueManager(character='rude_banker')
    response = dm.get_response(intent, context)

    # Step 4: Generate text response (NLG)
    bot_response = generate_response(response, context)

    # Step 5: Convert text response to speech (TTS)
    text_to_speech(bot_response)

if __name__ == "__main__":
    main()
import speech_recognition as sr

def speech_to_text():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        print("Adjusting for background noise...")
        recognizer.adjust_for_ambient_noise(source)
        print("Listening now...")
        audio = recognizer.listen(source)

    try:
        print("Recognizing speech...")
        text = recognizer.recognize_google(audio, language="en-US")  # Change language as needed
        print(f"User said: {text}")
        return text
    except Exception as e:
        print(f"Error recognizing speech: {e}")
        return ""
from transformers import pipeline

def analyze_intent(text):
    # Load a pre-trained NLU model from Hugging Face
    nlu_model = pipeline("text-classification", model="distilbert-base-uncased")

    # Analyze intent
    result = nlu_model(text)
    intent = result[0]['label']
    context = {}  # Extract context if needed
    return intent, context
    class DialogueManager:
    def __init__(self, character):
        self.character = character

    def get_response(self, intent, context):
        if self.character == 'rude_banker':
            return self._rude_banker_response(intent)
        elif self.character == 'humble_actor':
            return self._humble_actor_response(intent)

    def _rude_banker_response(self, intent):
        if intent == 'greet':
            return "Yeah, what do you want?"
        elif intent == 'query':
            return "Check our website; I'm busy."
        else:
            return "Not my problem."

    def _humble_actor_response(self, intent):
        if intent == 'greet':
            return "Hello! How can I help my dear fan today?"
        elif intent == 'query':
            return "Oh, I'm so happy you asked! Let me explain."
        else:
            return "I'm not sure, but I'd love to find out for you."
from transformers import pipeline

def generate_response(intent_response, context):
    # Using GPT for text generation
    nlg_model = pipeline("text-generation", model="gpt2")
    generated_text = nlg_model(intent_response, max_length=50)
    return generated_text[0]['generated_text']
    from gtts import gTTS
import os

def text_to_speech(text):
    tts = gTTS(text=text, lang='en')
    tts.save("response.mp3")
    os.system("start response.mp3")  # Use 'afplay' on macOS or 'start' on Windows


