# Wmanga.plus
Чтение популярной манги и манхвы можно прочитать здесь в Wmanga+ бесплатно и на русском 
<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Wmanga</title>

<style>

body{
background:#0f172a;
color:white;
font-family:Arial;
margin:0;
}

header{
background:#111827;
padding:15px;
display:flex;
justify-content:space-between;
align-items:center;
}

.logo{
font-size:24px;
color:#ef4444;
font-weight:bold;
}

nav a{
color:white;
margin:0 10px;
text-decoration:none;
}

.hero{
padding:60px;
text-align:center;
background:#1f2937;
}

.hero h1{
font-size:40px;
}

.button{
background:#ef4444;
padding:12px 20px;
border-radius:8px;
display:inline-block;
margin-top:20px;
}

.manga-grid{
display:grid;
grid-template-columns:repeat(2,1fr);
gap:20px;
padding:20px;
}

.card{
background:#1e293b;
padding:15px;
border-radius:10px;
}

.card img{
width:100%;
border-radius:10px;
}

</style>

</head>

<body>

<header>

<div class="logo">Wmanga</div>

<nav>
<a href="#">Главная</a>
<a href="#">Каталог</a>
<a href="#">Популярное</a>
<a href="#">Вход</a>
</nav>

</header>

<section class="hero">

<h1>Читай мангу и манхву бесплатно</h1>

<div class="button">Начать читать</div>

</section>

<section class="manga-grid">

<div class="card">
<img src="https://i.imgur.com/Z7AzH2c.jpeg">
<h3>Solo Leveling</h3>
</div>

<div class="card">
<img src="https://i.imgur.com/4AiXzf8.jpeg">
<h3>Romance Emperor</h3>
</div>

<div class="card">
<img src="https://i.imgur.com/Z7AzH2c.jpeg">
<h3>Magic Knight</h3>
</div>

<div class="card">
<img src="https://i.imgur.com/4AiXzf8.jpeg">
<h3>Demon Girl</h3>
</div>

</section>

</body>
</html>