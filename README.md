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