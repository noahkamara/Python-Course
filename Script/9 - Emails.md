# 9 Emails



## 9.1 Sending Emails

#### 9.1.1 Templates

"Template Strings" enthalten Platzhalter, die mit anderen Strings ausgetauscht werden können. ``${identifier}``

> Hallo ${RECEIVER_NAME}, 
> 
> Dies ist eine Test-Nachricht.
> Das ist deine Email: ${RECEIVER_MAIL}
> 
> Liebe Grüße,
> ${SENDER_NAME}

Wir können einen String in ein Template verwandeln, indem wir mit ihm eine Entität der ``Template``-Klasse initialisieren

```python
from string import Template

string = """
Hallo ${RECIPIENT_NAME}, 

Dies ist eine Test-Nachricht.
Das ist deine Email: ${RECIPIENT_MAIL}

Liebe Grüße,
${SENDER_NAME}
"""

template = Template(string)
```



#### 9.1.2 SMTP

SMTP steht für Simple Mail Transfer Protocol und wird von Mail-Servern zum Senden von Emails genutzt. Das Package ``smtplib`` implementiert dieses Protokoll für Python

```python
import smtplib
# set up the SMTP server
s = smtplib.SMTP(host='your_host_address_here', port=your_port_here)
s.starttls()
s.login(MY_ADDRESS, PASSWORD)
```



#### 9.1.3 Das Email Package

Das Email Package kann zum erstellen von Mails verwendet werden. (Um den gängigen Standards für Mails gerecht zu werden)

```python
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText


msg = MIMEMultipart()
# META-Daten der Email setzen
msg['From'] = SENDER_ADDRESS
msg['To'] = RECIPIENT_ADRESS
msg['Subject']="This is TEST"

msg.attach(MIMEText(MESSAGE, 'plain')) # Den Text hinzufügen 

    
```



#### 9.1.4 Mails versenden

```
import smtplib

from string import Template

from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText

class Contact:
    def __init__(self, last, first, adress):
        self.last_name = last
        self.first_name = first
        self.email = adress


def connectServer(host, port, adress, password):
    """Returns a Server connection.

    Arguments:
        host {str} -- hostname of the server
        port {int} -- port for the server
        adress {str} -- email adress of the account
        password {str} -- password of the account

    Returns:
        smtplib.SMTP -- SMTP-Server-Client
    """    
    s = smtplib.SMTP(host=host, port=port)
    s.starttls()
    s.connect(host=host, port=port)
    s.login(adress, password)
    
    return s

def loadTemplate(filename):
    """Loads a Template file from Disk
    
    Arguments:
        filename {str} -- path of the file
    
    Returns:
        Template -- String Template object
    """    
    with open(filename, 'r', encoding='utf-8') as template_file:
        template_file_content = template_file.read()
    return Template(template_file_content)

def getContacts(filename):
    """retrieves the contacts from a CSV file
    
    Arguments:
        filename {str} -- path of the file

    Returns:
        list[Contact] -- list of Contact entities
    """    
    contacts = []
    with open(filename, mode='r') as f:
        for contact in f:
            contacts.append( Contact(contact[0],contact[1],contact[2]) )
    return contacts

def makeMessage(sender, recipient, subject, msg_body, msg_type):
    """Generates and returns a MIMEMultipart Message
    
    Arguments:
        sender {str} -- Sender's Email
        recipient {str} -- Recipient's Email
        subject {str} -- Email Subject
        msg_body {str} -- Email body
        msg_type {str} -- The type of the body (e.g. 'plain' or 'html')
    
    Returns:
        MIMEMultipart -- MIMEMultipart Message
    """    
    msg = MIMEMultipart()
    msg['From'] = sender
    msg['To'] = recipient
    msg['Subject'] = subject
    msg.attach(MIMEText(msg_body, msg_type))
    return msg


# Server Config
cfg = {
    'host': 'smtp.ionos.de',
    'port': 587,
    'user': 'test@dev.noahkamara.com',
    'pass': ''
}


# Initialize Server-Client
server = connectServer(cfg['host'], cfg['port'], cfg['user'], cfg['pass'])

# Retrieve Contacts
# contacts = getContacts('contacts.csv')
contacts = [
    Contact('Kamara','Test01','test01@login.noahkamara.com'),
    Contact('Kamara','Test02','test02@login.noahkamara.com'),
    Contact('Kamara','Test03','test03@login.noahkamara.com'),
    Contact('Kamara','Test04','test04@login.noahkamara.com')
]

# For each contact
for contact in contacts:
    # Load template
    template = loadTemplate('live/template')
    # substitute placehoders in template
    msg_body = template.substitute(RECIPIENT_NAME=contact.first_name, RECIPIENT_MAIL=contact.email, SENDER_NAME='Python Script')
    msg_type = 'plain' # Message Type
    # make a MIMEMUltipart message
    msg = makeMessage(cfg['user'], contact.email, f"Hallo {contact.first_name}", msg_body, msg_type)
    # Send Message
    server.send_message(msg)

# Close server Connection
server.quit()
```

