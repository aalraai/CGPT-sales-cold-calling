# ColdCallGenius
### Practice realistic cold calls with an AI bot

▶️ _Try it now! call 864-387-2800 and test out your best pitch!_

📲 This app uses a twilio number to point to a python script running on ngrok server

📱 Twilio configuration for phone number, ngrok URL endpoint

💻 python start.py script runs as system service 

🕸️ ngrok starts server pointing to coldcallgenius.ngrok.io

🏃 Services restart and run while server is up

---

# Cold Call Genius Setup Playbook
### Overview

This playbook guides you through the setup process of the Cold Call Genius application, a voice chatbot that leverages Twilio, Deepgram, and OpenAI (GPT-4) to conduct phone conversations. Follow these steps to replicate the setup for future instances.

The source code and necessary files for the setup can be found in the [GitHub repository](https://github.com/kevingduck/ColdCallGenius)

### Prerequisites
Before you start, make sure you have *Python 3.10+* installed on your system.
#### Necessary API keys:
Twilio API Key
OpenAI API Key (for GPT-4)
Deepgram API Key

## Steps
### Clone the Repository

Clone the [Cold Call Genius GitHub repository](https://github.com/kevingduck/ColdCallGenius) to your local system or server.

`
git clone https://github.com/kevingduck/ColdCallGenius.git
`

### Install Dependencies
Navigate to the cloned repository and install the necessary Python packages. You can find these in the requirements.txt file.

```
cd ColdCallGenius
pip install -r requirements.txt
```

### Configure Environment Variables

Create a .env file in the repository folder and set the following environment variables with your API keys:

```
OPENAI_API_KEY=your_openai_api_key
CONSOLE_API_KEY=your_deepgram_api_key
TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
```

### Setup Twilio

Set up a Twilio account and obtain a Twilio phone number.

In the Twilio console, configure the webhook endpoint to point to the URL generated by ngrok (detailed in the next step) followed by /twilio/twiml/start. For instance, http://your-ngrok-subdomain.ngrok.io/twilio/twiml/start.

### Setup ngrok

Install ngrok on your system or server.

Authenticate following the (steps here)[https://dashboard.ngrok.com/get-started/setup].

Run the following command to create a publicly accessible URL that forwards to your local server on port 8080:

```
ngrok http --domain coldcallgenius.com 8080
```

At this point you should be able to run `python3 start.py` in one terminal and `ngrok http 8080` in another. In the Twilio console, set the endpoint as the ngrok URL, e.g., http://<ngrok-url.io>/twilio/twiml/start. You should be able to call the number and see it register on the ngrok terminal and also see the python script responding. 

Continue on to make it more resilient with system services. A $10/mo subscription to ngrok is recommended as it gets you a static domain that doesn't go down (otherwise setup an apahce/ nginx server).

### Create System Services

Create system services to manage the Python script (start.py) and the ngrok script. These services will ensure that the scripts run continuously and restart in case of failures or system restarts.

Before we begin, ensure you have necessary packages installed. You might require systemd and python3 (along with pip to install dependencies from the repository).

```
sudo apt update
sudo apt install systemd python3 python3-pip
```

Navigate to the project directory

```
cd ColdCallGenius
```

Inside the repository folder, install the necessary Python dependencies using pip. It's recommended to do this in a virtual environment:

```
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Next, create a systemd service file to manage the start.py script. Open a new file in the /etc/systemd/system directory (you'll need root permissions):

```
sudo nano /etc/systemd/system/coldcallgenius.service
```

In the editor, fill out the service file with the following details:

```
[Unit]
Description=Cold Call Genius Service
After=network.target

[Service]
User=yourusername
WorkingDirectory=/path/to/your/repository/ColdCallGenius
ExecStart=/path/to/your/repository/ColdCallGenius/venv/bin/python /path/to/your/repository/ColdCallGenius/start.py
Restart=always

[Install]
WantedBy=multi-user.target
```

Replace /path/to/your/repository with the actual path where the repository is cloned and yourusername with your Linux username.

Now, enable and start the service to make it run on boot:

```
sudo systemctl enable coldcallgenius.service
sudo systemctl start coldcallgenius.service
```

You can verify if the service is running successfully by checking its status:

```
sudo systemctl status coldcallgenius.service
```

### Testing

Once the script and ngrok are running, call the twilio number to test it out. 

### Logs

You can use the journalctl command to view the logs of your service. To watch the logs in real time, use the -f flag, which tells journalctl to follow the log output, similar to tail -f:

```
sudo journalctl -u coldcallgenius.service -f
```
