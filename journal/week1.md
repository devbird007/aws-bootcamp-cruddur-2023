# Week 1 — App Containerization

## Containerize the Backend
### Run Python
```
cd backend-flask
pip3 install -r requirements.txt
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd ..
```

- Make sure to unlock the port `4567` in the port tab of your VSCode
- Open the link for `4567` in your browser
- Append to the url `/api/activities/home`
- You should get back json
