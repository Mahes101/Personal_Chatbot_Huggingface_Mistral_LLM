# Personal_Chatbot_Huggingface_Mistral_LLM
Building a personal chatbot using HuggingFace Spaces, HuggingFace Inference Endpoints, LangChain, and Streamlit.

We’ll use the powerful mistralai/Mistral-7B-Instruct-v0.3 model from HuggingFace to generate responses and Streamlit to create a user-friendly interface.

Prerequisites
Familiarity with git repositories.
Git installed locally and credentials saved.
Familiarity with Python.
HuggingFace.co Account (see instructions below).
Code editor installed locally, such as VS Code.

HuggingFace.co Account
You will need to Sign Up for a HuggingFace.co account.

Getting Permission to Use Mistral Instruct
In order to use some models on HuggingFace, we need to be granted permission by the owner of the model. This invovles clicking on a button to request permission, reading through the agreement, and clicking Accept.

Navigate to https://huggingface.co/mistralai/Mistral-7B-Instruct-v0.3
Click on the Agree and access repository to be granted access.

You will instantly be granted permission and the Gated Model a portion of the model's card should now display a message that you've been granted access.

Create a New Space
Once you’ve logged in to your HuggingFace.co account:

Click on your profile icon in the top right corner.
Select + New Space.
A new page will open for creating the Space that will host our Streamlit application.

Enter the name of your space. (Notice that it will be associated with your username by default.)
Select a License (this author selected the MIT license).
Select Streamlit option for the SpaceSDK.
We will be using the default Space hardware, since it is free.
Click Create Space.
Clone the Space Locally
A new page will open with instructions for cloning the Space’s repository.

Take note of the instructions highlighted below for creating your login token for HuggingFace.
Copy the Git Clone command displayed and clone your repo locally.
Open the Space’s repository in a code editor like VS Code.
Repository Structure
Requirements
Before we start coding, we need to make sure we have the necessary Python packages installed. We will need to provide the same list of packages as part of the Space, so we manage these dependencies using a requirements.txt file. Here’s the one we'll use for this project:

Create a requirements.txt file in the repository with the required packages.
Note: using exact versions in your requirements files is often a good idea. However, due to the varied nature of people’s operating systems and previous environments, we will install the latest versions by default.

transformers
huggingface_hub
streamlit
langchain_core
langchain_community
langchain_huggingface
langchain_text_splitters
accelerate
watchdog
tqdm
Install the requirements.txt
Note: it is strongly recommended to create a new environment for this project. See my previous blog post: Creating Anaconda Environments for Data Scientists

Activate your environment.
Run the following command in your terminal to install the required packages:
pip install -r requirements.txt
This will ensure that all the necessary libraries are installed, making it easy to replicate and deploy the chatbot environment.

App
Create a new .py file called app.py.
Setting Up the Model
We’ll use the mistralai/Mistral-7B-Instruct-v0.3 model from HuggingFace for text generation. This model is well-suited for conversational AI tasks and can handle various user queries with ease, as long as we make sure to use the prompt formatting suggested on the model card. Specifically, we will wrap the prompt and user queries with "[INST] [/INST]".
HuggingFaceEndpoint We use the HuggingFaceEndpoint class from langchain_huggingface to query the chosen model. This requires our API token, which we have saved as HF_TOKEN (Details further below.)
Creating the Chatbot Interface
We’ll use Streamlit to create a simple web interface where users can interact with the chatbot. The interface will include settings for customizing the system message, avatars, and more.

Streamlit Setup
First, let’s set up the basic configuration for the Streamlit app:

import streamlit as st
​
# Configure the Streamlit app
st.set_page_config(page_title="Personal ChatBot", page_icon="🤖", layout="wide")
st.title("Personal (Free) HuggingFace ChatBot")
st.markdown("*This is a simple chatbot that uses the HuggingFace transformers library transformers library to generate responses to your text input. It uses the meta-llama/Meta-Llama-3.1-8B-Instruct.*")
Session State Initialization
Next, we initialize the session state to store various parameters and user inputs. When using the session_state with streamlit, each entry must be declared early in the app.py.
Session State: Using the session state is vital for a Public application, as it gives each instance of your app its own stored variables. If we stored the information in variables outside the session_state, it would be changed for every user when one user changes a value. The chat history would also be shared, which is not what we want.
Sidebar Configuration
The sidebar allows users to customize the chatbot’s settings, including the system message, starting message, max response length, and avatars.
Sidebar: Users can customize the chatbot’s system message, starting message, max response length, and avatars via the sidebar.
Columns: We use columns to organize the avatar selection, providing a clean and intuitive interface.
Chat History Initialization
Initialize or reset the chat history:

# Initialize or reset chat history
if "chat_history" not in st.session_state or reset_history:
    st.session_state.chat_history = [{"role": "assistant", "content": st.session_state.starter_message}]
Generating Responses
The get_response function generates a response from the chatbot based on the user's input. It utilizes the language model and incorporates the conversation context. Take note of the “[INST]” and “[/INST]” tags around the prompt text. Without this, the model would struggle to know if it was being given instructions to follow or starter text to complete.
Prompt Template: We create a prompt template that incorporates the system message, conversation history, and user input to guide the model’s response generation.
Output Parser: The StrOutputParser extracts the generated content from the model's output.
Chat History: The chat history is updated with each new message, ensuring context is maintained throughout the conversation.
Configure the Chat Interface
Chat Interface: We use Streamlit’s chat input feature to capture user input and display chat messages with appropriate avatars. We created the output container first in order to prevent the interface from reloading with every new input.
Displaying Chat Messages
Finally, we’ll set up the chat interface to display messages and allow user input:
Message Display: Messages are displayed sequentially, maintaining the conversation history. New user messages are displayed immediately before calling the LLM.
Waiting for Generation
We used a spinner in our chat interface to inform the user that the chatbot is generating its response.
Once the response has been generated, it will be displayed in the same location. Note that we are not using streaming for this application, so all of the text will appear at once.

Testing the App Locally
Log In to HuggingFace Hub to Access Model
Save the access token your created for git earlier inside a text file named hugging-face.txtin a .secret/ folder inside of your User folder. (e.g. ~/.secret/hugging-face.txt)
Then, open your ~/.bash_profile (or ~/.zshrc) file and add the following command:
export HF_TOKEN=$(cat ~/.secret/hugging-face.txt)
Open a New Terminal window so that your new HF_TOKEN is loaded.
Before running your app for the first time, you must log in to huggingface_hub. Run the following command from your Terminal and then enter your username and password when prompted.
huggingface-cli login
Once you’ve run the command above the credentials will be saved on your local machine and you will not need to repeat this command.

Start Your App
Activate your env in your Terminal before starting the app with:
streamlit run app.py
A new browser window will open with your app. Have a test conversation to ensure everything is operating as expected.
Deploying the Chatbot
To deploy your chatbot on HuggingFace Spaces, follow these steps:

1) Upload your code: Ensure all the necessary files, including the Streamlit app code and any dependencies, are committed to your HuggingFace Space repository.
Reminder: you will need to use your HuggingFace username and an access token as your git credentials when you go to push your code.
If using VS Code, you will see a small pop-up box appear for your user name and then your password (your HuggingFace token).



2) Deploy: HuggingFace Spaces will automatically detect the streamlit command and launch your app.
3) Launch your Space by navigatig to your Proile page on HuggingFace.co and you should see your Space as a large button. You will also see the status of the Space in the top-left corner.

4) Click on your Space and then click on its Settings in the top-right corner.

5) Scroll down to the Variables and Secrets section and click New Secret
6) Enter your access token as HF_TOKEN and save it. This will allow you to access the gated model.

Once the build is complete, you’ll have a fully functional chatbot that can be accessed and interacted with online.

Conclusion
Creating a personal chatbot using HuggingFace Spaces and Streamlit is a rewarding experience that showcases the power of modern AI models. By leveraging these tools, you can build a customized conversational agent to assist with a variety of tasks.

Feel free to explore the code and make modifications to suit your specific needs. Happy coding!
    
