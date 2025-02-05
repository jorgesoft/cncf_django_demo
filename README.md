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

### Add GitHub Actions flow for scanning

1. Add Bandit scanning

Add it from /actions/new?category=security or:

```
name: Bandit
on:
  push:
    branches: [ "main" ]
  pull_request:
    # The branches below must be a subset of the branches above
    branches: [ "main" ]
  schedule:
    - cron: '35 4 * * 4'

jobs:
  bandit:
    permissions:
      contents: read 
      security-events: write 
      actions: read 

    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Bandit Scan
        uses: shundor/python-bandit-scan@9cc5aa4a006482b8a7f91134412df6772dbda22c
        with: # optional arguments
          path: "./cncf_demo"
          
          exit_zero: true
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} 
```
Make sure to add "path: "./cncf_demo" under -steps.with

2. Add OSV Scanning

Add it from /actions/new?category=security or:

```
name: OSV-Scanner

on:
  pull_request:
    branches: [ "main" ]
  merge_group:
    branches: [ "main" ]
  schedule:
    - cron: '21 4 * * 1'
  push:
    branches: [ "main" ]

permissions:
  security-events: write
  contents: read

jobs:
  scan-scheduled:
    if: ${{ github.event_name == 'push' || github.event_name == 'schedule' }}
    uses: "google/osv-scanner-action/.github/workflows/osv-scanner-reusable.yml@1f1242919d8a60496dd1874b24b62b2370ed4c78" # v1.7.1
    with:
      # Example of specifying custom arguments
      scan-args: |-
        -r
        --skip-git
        ./
  scan-pr:
    if: ${{ github.event_name == 'pull_request' || github.event_name == 'merge_group' }}
    uses: "google/osv-scanner-action/.github/workflows/osv-scanner-reusable-pr.yml@1f1242919d8a60496dd1874b24b62b2370ed4c78" # v1.7.1
    with:
      # Example of specifying custom arguments
      scan-args: |-
        -r
        --skip-git
        ./
```

### Fix Django version

Set `Django==4.0.1` to test dependancy scanning.

Use `Django==5.1.5` to get the latest version.


### Fix secret in code

Add `import os`

Set `SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]`

Run the commad: `export DJANGO_SECRET_KEY="your-very-secure-secret-key"`
