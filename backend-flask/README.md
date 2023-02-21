# FREE AWS Cloud Project Bootcamp

FRONTEND_URL="*" BACKEND_URL="*" docker run --rm -p 4567:4567 -it backend-flask

docker run --rm -p 4567:4567 -it  -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask
  
docker run --rm -p 4567:4567 =it backend-flask -e FRONTEND_URL='*' -e BACKEND_URL='*'

docker build -t  backend-flask ./backend-flask -e FRONTEND_URL='*' -e BACKEND_URL='*'

docker build -t backend-flask ./backend-flask
docker build -t backend-flask:latest ./backend-flask
# Install python version
```
pyenv install 3.10.9
```

# Set your python version
```
pyenv global 3.10.9
```

# Create virual environment
```
python -m venv venv
```

# Activate environment
```
source venv/bin/activate
```

# Install Flask
```
pip install flask
```