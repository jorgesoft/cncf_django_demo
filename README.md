# cncf_django_demo

### Start env:

virtualenv cncf
source cncf/bin/activate

### Install Django

echo "Django==4.0.0  # Vulnerable version with known issues" > requirements.txt
pip install -r requirements.txt
django-admin startproject cncf_demo
cd cncf_demo
python manage.py runserver

### Install and run bandit

pip install bandit
bandit -r ./

### Install and run nikto

sudo apt install nikto -y
nikto -host 127.0.0.1:8000 -output nikto.html

### Commit changes

Normal commit

### Sign your GitHub commits 

Make the key:

```
sudo apt install gnupg

# Generate a New GPG Key
gpg --full-generate-key

# List your generated keys
gpg --list-secret-keys --keyid-format=long

# Find the GPG key ID (a long hexadecimal number like 3AA5C34371567BD2).
# Export the public key:
gpg --armor --export YOUR_GPG_KEY_ID

# Copy the output.
```

Add GPG Key to GitHub

1. Go to GitHub → Settings → SSH and GPG Keys.
2. Click New GPG Key and paste the exported key.
3. Save it.

Configure Git to Sign Commits

```
git config --global user.signingkey YOUR_GPG_KEY_ID
git config --global commit.gpgsign true

# Add GPG agent for easier signing:
echo "use-agent" >> ~/.gnupg/gpg.conf
echo "pinentry-mode loopback" >> ~/.gnupg/gpg.conf

# Make a test commit
git add README.md
git commit -S -m "Signed commit test"
git log --show-signature -1
```