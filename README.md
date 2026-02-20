# Deep-Learning-Fundus-Image-Analysis-for-Early-Detection-Diabetic-Retinopathy
Deep learning–based system for early detection of Diabetic Retinopathy using retinal fundus images. The model uses CNN and transfer learning to classify disease severity levels, assisting ophthalmologists with faster, accurate screening and reducing risk  vision loss through timely diagnosis.
app.py (Flask + Deep Learning Prediction)
from flask import Flask, render_template, request, redirect, session
import numpy as np
import os
from tensorflow.keras.models import load_model
from tensorflow.keras.preprocessing.image import load_img, img_to_array
from werkzeug.utils import secure_filename

app = Flask(__name__)
app.secret_key = "dr_project_key"

UPLOAD_FOLDER = "uploads"
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER

# Load trained model
model = load_model("Updated-Xception-diabetic-retinopathy.h5")

classes = ['No DR','Mild','Moderate','Severe','Proliferative DR']

# ---------------- HOME ----------------
@app.route('/')
def home():
    return render_template("home.html")

# ---------------- LOGIN ----------------
@app.route('/login', methods=['GET','POST'])
def login():
    if request.method == 'POST':
        if request.form['username']=="admin" and request.form['password']=="1234":
            session['user']="admin"
            return redirect('/upload')
        else:
            return render_template("login.html",msg="Invalid Login")
    return render_template("login.html")

# ---------------- REGISTER ----------------
@app.route('/register', methods=['GET','POST'])
def register():
    if request.method=='POST':
        return render_template("login.html",msg="Registered Successfully!")
    return render_template("register.html")

# ---------------- LOGOUT ----------------
@app.route('/logout')
def logout():
    session.pop('user',None)
    return redirect('/')

# ---------------- UPLOAD ----------------
@app.route('/upload')
def upload():
    if 'user' not in session:
        return redirect('/login')
    return render_template("upload.html")

# ---------------- PREDICT ----------------
@app.route('/predict',methods=['POST'])
def predict():

    file=request.files['file']
    filename=secure_filename(file.filename)
    filepath=os.path.join(app.config['UPLOAD_FOLDER'],filename)
    file.save(filepath)

    # preprocessing
    img=load_img(filepath,target_size=(224,224))
    img=img_to_array(img)/255.0
    img=np.expand_dims(img,axis=0)

    pred=model.predict(img)
    result=classes[np.argmax(pred)]
    confidence=round(np.max(pred)*100,2)

    return render_template("result.html",prediction=result,conf=confidence,img=filename)

# ---------------- RUN ----------------
if __name__=="__main__":
    app.run(debug=True)
🎨 static/style.css
body{
    font-family:Arial;
    background:#f2f6fc;
    margin:0;
}

.navbar{
    background:#0b3d91;
    color:white;
    padding:15px;
    text-align:center;
    font-size:22px;
}

.container{
    text-align:center;
    margin-top:30px;
}

.card{
    background:white;
    padding:25px;
    margin:auto;
    width:350px;
    border-radius:10px;
    box-shadow:0 0 10px gray;
}

button{
    padding:10px 20px;
    background:#0b3d91;
    color:white;
    border:none;
    border-radius:5px;
}

img{
    border-radius:10px;
    margin-top:15px;
}
🏠 templates/home.html
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="{{url_for('static',filename='style.css')}}">
</head>
<body>

<div class="navbar">Diabetic Retinopathy Detection</div>

<div class="container">
<h2>Welcome to DR Prediction System</h2>

<img src="{{url_for('static',filename='normal.jpg')}}" width="300">
<img src="{{url_for('static',filename='dr.jpg')}}" width="300">

<br><br>
<a href="/login"><button>Login</button></a>
<a href="/register"><button>Register</button></a>
</div>

</body>
</html>
🔐 templates/login.html
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="{{url_for('static',filename='style.css')}}">
</head>
<body>

<div class="container">
<div class="card">
<h2>Login</h2>
<form method="POST">
<input type="text" name="username" placeholder="Username"><br><br>
<input type="password" name="password" placeholder="Password"><br><br>
<button type="submit">Login</button>
</form>
<p>{{msg}}</p>
</div>
</div>

</body>
</html>
📝 templates/register.html
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="{{url_for('static',filename='style.css')}}">
</head>
<body>

<div class="container">
<div class="card">
<h2>Register</h2>
<form method="POST">
<input type="text" placeholder="Name"><br><br>
<input type="text" placeholder="Username"><br><br>
<input type="password" placeholder="Password"><br><br>
<button type="submit">Register</button>
</form>
</div>
</div>

</body>
</html>
📤 templates/upload.html
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="{{url_for('static',filename='style.css')}}">
</head>
<body>

<div class="container">
<div class="card">
<h2>Upload Retina Image</h2>
<form action="/predict" method="POST" enctype="multipart/form-data">
<input type="file" name="file"><br><br>
<button type="submit">Predict</button>
</form>
<a href="/logout">Logout</a>
</div>
</div>

</body>
</html>
📊 templates/result.html
<!DOCTYPE html>
<html>
<head>
<link rel="stylesheet" href="{{url_for('static',filename='style.css')}}">
</head>
<body>

<div class="container">
<div class="card">

<h2>Prediction Result</h2>

<img src="/uploads/{{img}}" width="250"><br><br>

<h3>Disease: {{prediction}}</h3>
<h3>Confidence: {{conf}}%</h3>

{% if prediction != 'No DR' %}
<h2 style="color:red;">Consult Doctor</h2>
{% else %}
<h2 style="color:green;">Healthy Eye</h2>
{% endif %}

</div>
</div>

</body>
</html>
🖥 FINAL OUTPUT SCREENS
1️⃣ Home Page

Shows normal vs DR images + login/register buttons

2️⃣ Login

Enter:
admin / 1234

3️⃣ Upload

Upload retina image

4️⃣ Result Example
Prediction Result
Disease: Moderate
Confidence: 93.4%

Consult Doctor
▶️ Run
pip install flask tensorflow pillow numpy
python app.py

Open browser:

http://127.0.0.1:5000


# Diabetic Retinopathy Flask Project

## 1. app.py

```python
from flask import Flask, render_template, request, redirect, url_for, session
import numpy as np
from tensorflow.keras.models import load_model
from tensorflow.keras.preprocessing.image import load_img, img_to_array
import os

app = Flask(__name__)
app.secret_key = "dr_secret"

model = load_model("Updated-Xception-diabetic-retinopathy.h5")

UPLOAD_FOLDER = 'static/uploads'
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER

classes = ['No DR','Mild','Moderate','Severe','Proliferative DR']

@app.route('/')
def home():
    return render_template('home.html')

@app.route('/login', methods=['GET','POST'])
def login():
    if request.method=='POST':
        user=request.form['username']
        pwd=request.form['password']
        if user=='admin' and pwd=='admin':
            session['user']=user
            return redirect(url_for('predict'))
        else:
            return render_template('login.html',msg='Invalid Credentials')
    return render_template('login.html')

@app.route('/logout')
def logout():
    session.pop('user',None)
    return redirect(url_for('home'))

@app.route('/predict', methods=['GET','POST'])
def predict():
    if 'user' not in session:
        return redirect(url_for('login'))

    if request.method=='POST':
        file=request.files['file']
        path=os.path.join(app.config['UPLOAD_FOLDER'],file.filename)
        file.save(path)

        img=load_img(path,target_size=(224,224))
        img=img_to_array(img)/255.0
        img=np.expand_dims(img,axis=0)

        pred=model.predict(img)
        result=classes[np.argmax(pred)]

        return render_template('predict.html',result=result,img=path)

    return render_template('predict.html')

if __name__=='__main__':
    app.run(debug=True)
```

---

## 2. templates/home.html

```html
<!DOCTYPE html>
<html>
<head>
<title>DR Home</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="navbar">
<h2>Diabetic Retinopathy Classification</h2>
<a href="/login">Login</a>
</div>

<div class="container">
<h1>Upload Retina Image to Detect DR</h1>
<img src="{{ url_for('static', filename='eye.png') }}" class="hero">
</div>
</body>
</html>
```

---

## 3. templates/login.html

```html
<!DOCTYPE html>
<html>
<head>
<title>Login</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="login-box">
<h2>Login</h2>
<form method="post">
<input type="text" name="username" placeholder="Username" required>
<input type="password" name="password" placeholder="Password" required>
<button type="submit">Login</button>
</form>
<p>{{msg}}</p>
</div>
</body>
</html>
```

---

## 4. templates/predict.html

```html
<!DOCTYPE html>
<html>
<head>
<title>Prediction</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="navbar">
<a href="/logout">Logout</a>
</div>

<div class="container">
<h2>Upload Retina Image</h2>
<form method="post" enctype="multipart/form-data">
<input type="file" name="file" required>
<button type="submit">Predict</button>
</form>

{% if result %}
<h1>Prediction: {{result}}</h1>
<img src="/{{img}}" class="result-img">
{% endif %}
</div>
</body>
</html>
```

---

## 5. static/style.css

```css
body{
font-family: Arial;
background:#f4f4f4;
text-align:center;
}
.navbar{
background:#333;
color:white;
padding:15px;
}
.navbar a{
color:white;
float:right;
margin-right:20px;
text-decoration:none;
}
.container{
margin-top:40px;
}
.login-box{
width:300px;
margin:100px auto;
padding:20px;
background:white;
border-radius:10px;
box-shadow:0 0 10px gray;
}
input,button{
width:90%;
padding:10px;
margin:10px;
}
button{
background:#28a745;
color:white;
border:none;
}
.result-img{
width:250px;
margin-top:20px;
}
.hero{
width:400px;
}
```


# Diabetic Retinopathy Flask Project

## 1. app.py

```python
from flask import Flask, render_template, request, redirect, url_for, session
import numpy as np
from tensorflow.keras.models import load_model
from tensorflow.keras.preprocessing.image import load_img, img_to_array
import os

app = Flask(__name__)
app.secret_key = "dr_secret"

model = load_model("Updated-Xception-diabetic-retinopathy.h5")

UPLOAD_FOLDER = 'static/uploads'
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER

classes = ['No DR','Mild','Moderate','Severe','Proliferative DR']

@app.route('/')
def home():
    return render_template('home.html')

@app.route('/login', methods=['GET','POST'])
def login():
    if request.method=='POST':
        user=request.form['username']
        pwd=request.form['password']
        if user=='admin' and pwd=='admin':
            session['user']=user
            return redirect(url_for('predict'))
        else:
            return render_template('login.html',msg='Invalid Credentials')
    return render_template('login.html')

@app.route('/logout')
def logout():
    session.pop('user',None)
    return redirect(url_for('home'))

@app.route('/predict', methods=['GET','POST'])
def predict():
    if 'user' not in session:
        return redirect(url_for('login'))

    if request.method=='POST':
        file=request.files['file']
        path=os.path.join(app.config['UPLOAD_FOLDER'],file.filename)
        file.save(path)

        img=load_img(path,target_size=(224,224))
        img=img_to_array(img)/255.0
        img=np.expand_dims(img,axis=0)

        pred=model.predict(img)
        result=classes[np.argmax(pred)]

        return render_template('predict.html',result=result,img=path)

    return render_template('predict.html')

if __name__=='__main__':
    app.run(debug=True)
```

---

## 2. templates/home.html

```html
<!DOCTYPE html>
<html>
<head>
<title>DR Home</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="navbar">
<h2>Diabetic Retinopathy Classification</h2>
<a href="/login">Login</a>
</div>

<div class="container">
<h1>Upload Retina Image to Detect DR</h1>
<img src="{{ url_for('static', filename='eye.png') }}" class="hero">
</div>
</body>
</html>
```

---

## 3. templates/login.html

```html
<!DOCTYPE html>
<html>
<head>
<title>Login</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="login-box">
<h2>Login</h2>
<form method="post">
<input type="text" name="username" placeholder="Username" required>
<input type="password" name="password" placeholder="Password" required>
<button type="submit">Login</button>
</form>
<p>{{msg}}</p>
</div>
</body>
</html>
```

---

## 4. templates/predict.html

```html
<!DOCTYPE html>
<html>
<head>
<title>Prediction</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="navbar">
<a href="/logout">Logout</a>
</div>

<div class="container">
<h2>Upload Retina Image</h2>
<form method="post" enctype="multipart/form-data">
<input type="file" name="file" required>
<button type="submit">Predict</button>
</form>

{% if result %}
<h1>Prediction: {{result}}</h1>
<img src="/{{img}}" class="result-img">
{% endif %}
</div>
</body>
</html>
```

---

## 5. static/style.css

```css
body{
font-family: Arial;
background:#f4f4f4;
text-align:center;
}
.navbar{
background:#333;
color:white;
padding:15px;
}
.navbar a{
color:white;
float:right;
margin-right:20px;
text-decoration:none;
}
.container{
margin-top:40px;
}
.login-box{
width:300px;
margin:100px auto;
padding:20px;
background:white;
border-radius:10px;
box-shadow:0 0 10px gray;
}
input,button{
width:90%;
padding:10px;
margin:10px;
}
button{
background:#28a745;
color:white;
border:none;
}
.result-img{
width:250px;
margin-top:20px;
}
.hero{
width:400px;
}
```

# Diabetic Retinopathy Flask Project

## 1. app.py

```python
from flask import Flask, render_template, request, redirect, url_for, session
import numpy as np
from tensorflow.keras.models import load_model
from tensorflow.keras.preprocessing.image import load_img, img_to_array
import os

app = Flask(__name__)
app.secret_key = "dr_secret"

model = load_model("Updated-Xception-diabetic-retinopathy.h5")

UPLOAD_FOLDER = 'static/uploads'
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER

classes = ['No DR','Mild','Moderate','Severe','Proliferative DR']

@app.route('/')
def home():
    return render_template('home.html')

@app.route('/login', methods=['GET','POST'])
def login():
    if request.method=='POST':
        user=request.form['username']
        pwd=request.form['password']
        if user=='admin' and pwd=='admin':
            session['user']=user
            return redirect(url_for('predict'))
        else:
            return render_template('login.html',msg='Invalid Credentials')
    return render_template('login.html')

@app.route('/logout')
def logout():
    session.pop('user',None)
    return redirect(url_for('home'))

@app.route('/predict', methods=['GET','POST'])
def predict():
    if 'user' not in session:
        return redirect(url_for('login'))

    if request.method=='POST':
        file=request.files['file']
        path=os.path.join(app.config['UPLOAD_FOLDER'],file.filename)
        file.save(path)

        img=load_img(path,target_size=(224,224))
        img=img_to_array(img)/255.0
        img=np.expand_dims(img,axis=0)

        pred=model.predict(img)
        result=classes[np.argmax(pred)]

        return render_template('predict.html',result=result,img=path)

    return render_template('predict.html')

if __name__=='__main__':
    app.run(debug=True)
```

---

## 2. templates/home.html

```html
<!DOCTYPE html>
<html>
<head>
<title>DR Home</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="navbar">
<h2>Diabetic Retinopathy Classification</h2>
<a href="/login">Login</a>
</div>

<div class="container">
<h1>Upload Retina Image to Detect DR</h1>
<img src="{{ url_for('static', filename='eye.png') }}" class="hero">
</div>
</body>
</html>
```

---

## 3. templates/login.html

```html
<!DOCTYPE html>
<html>
<head>
<title>Login</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="login-box">
<h2>Login</h2>
<form method="post">
<input type="text" name="username" placeholder="Username" required>
<input type="password" name="password" placeholder="Password" required>
<button type="submit">Login</button>
</form>
<p>{{msg}}</p>
</div>
</body>
</html>
```

---

## 4. templates/predict.html

```html
<!DOCTYPE html>
<html>
<head>
<title>Prediction</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
<div class="navbar">
<a href="/logout">Logout</a>
</div>

<div class="container">
<h2>Upload Retina Image</h2>
<form method="post" enctype="multipart/form-data">
<input type="file" name="file" required>
<button type="submit">Predict</button>
</form>

{% if result %}
<h1>Prediction: {{result}}</h1>
<img src="/{{img}}" class="result-img">
{% endif %}
</div>
</body>
</html>
```

---

## 5. static/style.css

```css
body{
font-family: Arial;
background:#f4f4f4;
text-align:center;
}
.navbar{
background:#333;
color:white;
padding:15px;
}
.navbar a{
color:white;
float:right;
margin-right:20px;
text-decoration:none;
}
.container{
margin-top:40px;
}
.login-box{
width:300px;
margin:100px auto;
padding:20px;
background:white;
border-radius:10px;
box-shadow:0 0 10px gray;
}
input,button{
width:90%;
padding:10px;
margin:10px;
}
button{
background:#28a745;
color:white;
border:none;
}
.result-img{
width:250px;
margin-top:20px;
}
.hero{
width:400px;
}
```
How to Run

Create folders:

project/
│── app.py
│── Updated-Xception-diabetic-retinopathy.h5
│
├── templates/
│   ├── home.html
│   ├── login.html
│   └── predict.html
│
├── static/
│   ├── style.css
│   └── uploads/

Install requirements

pip install flask tensorflow pillow numpy

Run

python app.py

Open browser

http://127.0.0.1:5000

Login credentials:

username: admin
password: admin
🖥️ Output You Will Get

Home Page

Title: Diabetic Retinopathy Classification

Login button

Login Page

Username & password form

Prediction Page

Upload retina image

Shows predicted class:

No DR
Mild
Moderate
Severe
Proliferative DR

📁 FINAL PROJECT STRUCTURE
Diabetic_Retinopathy/
│── app.py
│── Updated-Xception-diabetic-retinopathy.h5
│
├── templates/
│     home.html
│     login.html
│     register.html
│     upload.html
│     result.html
│
├── static/
│     style.css
│     normal.jpg
│     dr.jpg
│
├── uploads/


project team details
Team ID : LTVIP2026TMIDS71517

Team Size : 4

Team Leader : Charitha Vara

Team member : Kovela Kumari Markata

Team member : Bandi Divya Sree

Team member : Preethy Pothuraju

🟦 Technologies Used
🧠 Artificial Intelligence & Deep Learning

Deep Learning (CNN) – Detect retinal abnormalities automatically

Transfer Learning (Xception Model) – Improves accuracy using pretrained weights

TensorFlow & Keras – Model training and prediction framework

NumPy – Numerical operations on image arrays

🖼️ Image Processing

OpenCV / PIL (Pillow) – Load, resize, and preprocess fundus images

Image Normalization – Improve model performance

🌐 Web Application Development

Flask (Python Web Framework) – Backend server & model integration

HTML – Web page structure

CSS – UI styling

JavaScript – Image upload & prediction display

💾 Data & Storage

.h5 Model File – Saved trained CNN model

Dataset: Fundus Retina Images – Used for training and testing

🖥️ Development Environment

Python 3.x

Jupyter Notebook / VS Code

Anaconda / Pip Libraries
