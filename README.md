# Django Yubitest

A proof-of-concept for using [Yubikey OTP](https://developers.yubico.com/OTP/OTPs_Explained.html) as a required method for logging into a Django application. This example uses a locally-verified device, meaning the Yubikey device should be programmed via the Yubikey customization tool with well-known:

 - 4 byte modhex public ID (YubiCloud usually requires 6 by default)
 - 6 byte hex private ID
 - 16 byte hex secret key

Test vectors are used for demonstration purposes so no actual physical key is required.

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

For convenience you can just load the fixture data instead:

```bash
$ python manage.py loaddata initial_data.json
```

You should now be able to log in to http://127.0.0.1:8000/admin/login/ using user `admin`, password `admin123` and the OTP token `cccccccbdvgtiblfkbgturecfllberrvkinnctnn`
