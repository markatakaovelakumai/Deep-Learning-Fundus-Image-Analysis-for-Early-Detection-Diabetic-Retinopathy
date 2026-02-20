project executable files
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Diabetic Retinopathy Classification</title>

<style>
body{
    margin:0;
    font-family: Arial, sans-serif;
    background:#f5f5f5;
}
/* Top Header */
.header{
    background:#0f111a;
    color:white;
    padding:15px 30px;
    display:flex;
    justify-content:space-between;
    align-items:center;
}

.header h2{
    margin:0;
    font-weight:500;
}

.header a{
    color:white;
    text-decoration:none;
    margin-left:20px;
    font-size:14px;
}

/* Main Card */
.card{
    width:85%;
    margin:40px auto;
    background:white;
    padding:30px;
    border-radius:8px;
    box-shadow:0 5px 20px rgba(0,0,0,0.15);
    text-align:center;
}

/* Preview Circle */
.preview{
    width:160px;
    height:160px;
    margin:20px auto;
    border-radius:50%;
    overflow:hidden;
    border:6px solid #111;
}

.preview img{
    width:100%;
    height:100%;
    object-fit:cover;
}

/* Upload Section */
.upload{
    margin:15px 0;
}

button{
    padding:6px 15px;
    background:#dcdcdc;
    border:1px solid #999;
    cursor:pointer;
}

button:hover{
    background:#ccc;
}

/* Prediction Text */
.result{
    margin-top:20px;
    font-size:16px;
}

.result span{
    font-weight:bold;
}

/* Bottom Retina Images */
.samples{
    display:flex;
    justify-content:center;
    gap:30px;
    margin-top:30px;
}

.sample{
    width:150px;
    text-align:center;
}

.bg-green{
    background:#c8f2c8;
    padding:10px;
    border-radius:10px;
}

.bg-orange{
    background:#ffcc66;
    padding:10px;
    border-radius:10px;
}

.sample img{
    width:130px;
    height:130px;
    border-radius:50%;
    object-fit:cover;
}
</style>
</head>

<body>

<!-- Header -->
<div class="header">
    <h2>Diabetic Retinopathy Classification</h2>
    <div>
        <a href="#">Home</a>
        <a href="#">Logout</a>
    </div>
</div>

<!-- Main Card -->
<div class="card">

    <!-- Preview Image -->
    <div class="preview">
        <img src="https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcT9ucDMsBcryhQW-AZx0czdYNA3nmyyDI0_jbeBrxyRalzQfxdu">
    </div>

    <!-- Upload -->
    <div class="upload">
        <input type="file">
        <button>Submit</button>
    </div>

    <!-- Prediction -->
    <div class="result">
        Diabetic Retinopathy Classification is :
        <span>Proliferative DR</span>
    </div>

    <!-- Sample Images -->
    <div class="samples">

        <div class="sample">
            <div class="bg-green">
                <img src="https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcT9ucDMsBcryhQW-AZx0czdYNA3nmyyDI0_jbeBrxyRalzQfxdu">
            </div>
        </div>

        <div class="sample">
            <div class="bg-orange">
                <img src="https://www.researchgate.net/profile/Md-Ashraful-Islam-60/publication/337121927/figure/fig1/AS:823021930758144@1572655903391/Fundus-images-of-different-stages-of-diabetic-retinopathy.ppm">
            </div>
        </div>

        <div class="sample">
            <div class="bg-orange">
                <img src="https://upload.wikimedia.org/wikipedia/commons/3/3b/Diabetic_retinopathy.jpg">
            </div>
        </div>

        <div class="sample">
            <div class="bg-orange">
                <img src="https://www.researchgate.net/profile/Md-Ashraful-Islam-60/publication/337121927/figure/fig1/AS:823021930758144@1572655903391/Fundus-images-of-different-stages-of-diabetic-retinopathy.ppm">
            </div>
        </div>

        <div class="sample">
            <div class="bg-orange">
                <img src="https://upload.wikimedia.org/wikipedia/commons/3/3b/Diabetic_retinopathy.jpg">
            </div>
        </div>

    </div>

</div>

</body>
</html>
