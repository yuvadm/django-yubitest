# Django Yubitest

A proof-of-concept for using [Yubikey OTP](https://developers.yubico.com/OTP/OTPs_Explained.html) as a required method for logging into a Django application.

## Usage

The usual:

```bash
$ pip install -r requirements.txt
$ python manage.py migrate
```

Create an admin superuser:

```bash
$ python manage.py createsuperuser
```

Open a shell:

```bash
$ python manage.py shell
```

Then create a device for your user using one of the published [test vectors](https://developers.yubico.com/OTP/Test_Vectors.html):

```python
>>> from django.contrib.auth import get_user_model
>>> user = get_user_model().objects.get(username='admin')
>>> device = user.yubikeydevice_set.create(key='000102030405060708090a0b0c0d0e0f', private_id='010203040506', session=0, counter=0)
>>> device.public_id()
b'cccccccb'  # note the modhex encoded public id of the primary key (1)
```

You should now be able to log in to http://127.0.0.1:8000/admin/login/ using user `admin`, password `password` and the OTP token `cccccccbdvgtiblfkbgturecfllberrvkinnctnn`
