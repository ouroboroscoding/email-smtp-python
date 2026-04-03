# Email SMTP
Handles sending emails using SMTP

## Install
```
pip install email_smtp
```

## Requires
email_smtp requires python 3.10 or higher

## Contents
- [Configuration](#configuration)
- [Send](#send)
- [Error](#error)

## Configuration
This library requires the use of [`config_oc`](https://pypi.org/project/config-oc/)
to provide SMTP information and default email addresses.

| Key | Type | Optional | Description |
| --- | ---- | -------- | ----------- |
| email.error_to | string \| string[] | yes | The `to` address(es) to use if one is not passed to `em.error()`. |
| email.from | string | yes | The `from` address to use if one is not provided to `em.send()` or `em.error()`. |
| email.smtp | object | yes | The values required to setup a valid SMTP connection. |
| email.smtp.host | string | yes | The hostname of the SMTP server, defaults to "localhost". |
| email.smtp.port | integer | yes | The port of the SMTP server, defaults to 25. |
| email.smtp.tls | boolean | yes | Set to `true` to require TLS when connecting to the server. |
| email.smtp.user | string | yes | Must be set if the SMTP server requires a login. |
| email.smtp.passwd | string | yes | Must be set if the SMTP server requires a login. |

### Example configuration

```json
{
  "email": {
    "error_to": "errors@mydomain.com",
    "from": "noreply@mydomain.com",
    "smtp": {
      "host": "smtp.mydomain.com",
      "port": 587,
      "tls": true,
      "user": "smtpadmin@mydomain.com",
      "passwd": "password1234"
    }
  }
}
```

## Send

```python
from em import send, valid

def send_email(to, subject, text, html):

	if valid(to):
		send(to, subject, {
			'text': text,
			'html': html
		})
```

### Send Parameters
| Name | Type | Description |
| ---- | ---- | ----------- |
| `to` | str \| str[] | One or more email addresses to send the email to. |
| `subject` | str | The subject line of the email. |
| `opts` | dict | The parts of the email, like attachments and HTML content. |
| `opts.attachments` | str[] \| {'body': str, 'filename'}[] | A list of attachments to add to the email |
| `opts.bcc` | str \| str[] | One or more e-mail addresses to send a blind carbon copy to |
| `opts.cc` | str \| str[] | One or more e-mail addresses to send a carbo copy to |
| `opts.from` | str | The e-mail address to show as having sent the e-mail |
| `opts.html` | str | The HTML version of the e-mail to send, this or `text` must be set |
| `opts.reply-to` | str | The e-mail address to mark as receiving any replies |
| `opts.text` | str | The text version of the e-mail to send, this or `html` must be set |
| `opts.unsubscribe` | str | The URL to add to the header for the receiver to be able to unsubscribe |

## Error
`error` is simply a shortcut for using `config.email.error_to` with a simple text message

```python
import config
import em
import platform

# Calling error()
em.error('Error content')

# Is functionally equivalent to calling send()
#  with the following parameters
em.send(
	config.email.error_to(),
	'%s Error' % platform.node(),
	{ 'text': 'Error content' }
)