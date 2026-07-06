# Flask-4
import os
from flask import Flask, render_template, abort

app = Flask(__name__)

RECIPES = [
    {
        "id": 1,
        "title": "Osh (Palov)",
        "price": 45000,
        "description": "An'anaviy o'zbek palovi. Go'sht, sabzi va guruch uyg'unligi.",
        "ingredients": ["Guruch", "Go'sht", "Sabzi", "Piyoz", "Yog'", "Ziravorlar"]
    },
    {
        "id": 2,
        "title": "Manti",
        "price": 35000,
        "description": "Bug'da pishirilgan, sershira va mazali manti.",
        "ingredients": ["Xamir", "Go'sht yoki Qovoq", "Piyoz", "Dumba yog'i"]
    },
    {
        "id": 3,
        "title": "Somsa",
        "price": 10000,
        "description": "Tandirda yoki duxovkada pishgan qarsildoq somsa.",
        "ingredients": ["Katlama xamir", "Go'sht", "Piyoz", "Zira"]
    },
    {
        "id": 4,
        "title": "Chuchvara",
        "price": 30000,
        "description": "Issiqkina sho'rva bilan tortiladigan mayda chuchvaralar.",
        "ingredients": ["Xamir", "Qiymali farsh", "Piyoz", "Ko'katlar"]
    },
    {
        "id": 5,
        "title": "Uydagi non (Lochira)",
        "price": 0,  
        "description": "Issiq non yopish sirlari va retsepti bonus tariqasida taqdim etiladi.",
        "ingredients": ["Un", "Sut", "Sariyog'", "Tuz", "Droja"]
    }
]


@app.route('/')
def index():
    return render_template('index.html', recipes=RECIPES)


@app.route('/recipes/<int:recipe_id>')
def recipe_detail(recipe_id):
    recipe = next((r for r in RECIPES if r['id'] == recipe_id), None)
    if recipe is None:
        abort(404)  
    return render_template('detail.html', recipe=recipe)


TEMPLATE_DIR = os.path.join(os.path.dirname(__file__), 'templates')
os.makedirs(TEMPLATE_DIR, exist_ok=True)

BASE_HTML = """<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}Mening Retseptlarim{% endblock %}</title>
    <style>
        body { font-family: Arial, sans-serif; line-height: 1.6; margin: 0; padding: 20px; background: #f4f4f4; }
        .container { max-width: 800px; margin: auto; background: white; padding: 20px; border-radius: 8px; box-shadow: 0 0 10px rgba(0,0,0,0.1); }
        header { border-bottom: 2px solid #eee; padding-bottom: 10px; margin-bottom: 20px; }
        h1 { color: #333; }
        .recipe-card { border: 1px solid #ddd; padding: 15px; margin-bottom: 15px; border-radius: 5px; }
        .price { font-weight: bold; color: #2c3e50; }
        .free { color: #27ae60; font-weight: bold; }
        .btn { display: inline-block; background: #3498db; color: white; padding: 5px 10px; text-decoration: none; border-radius: 3px; }
        .btn:hover { background: #2980b9; }
        .back-link { display: inline-block; margin-top: 20px; text-decoration: none; color: #3498db; }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1><a href="/" style="text-decoration: none; color: #333;">Taomlar Retsepti Portali</a></h1>
        </header>

        <main>
            {% block content %} {% endblock %}
        </main>

        <footer style="margin-top: 30px; text-align: center; color: #777; font-size: 0.9em;">
            <p>&copy; 2026 Retseptlar Olami.</p>
        </footer>
    </div>
</body>
</html>"""

INDEX_HTML = """{% extends "base.html" %}

{% block title %}Bosh sahifa - Retseptlar Ro'yxati{% endblock %}

{% block content %}
    <h2>Bizning mazali retseptlarimiz</h2>

    {% for recipe in recipes %}
        <div class="recipe-card">
            <h3>{{ recipe.title }}</h3>
            <p>{{ recipe.description }}</p>
            <p>
                Narxi: 
                {% if recipe.price == 0 %}
                    <span class="free">Bepul</span>
                {% else %}
                    <span class="price">{{ recipe.price }} so'm</span>
                {% endif %}
            </p>
            <a href="/recipes/{{ recipe.id }}" class="btn">Retseptni ko'rish</a>
        </div>
    {% endfor %}
{% endblock %}"""

DETAIL_HTML = """{% extends "base.html" %}

{% block title %}{{ recipe.title }} - Retsept Tafsiloti{% endblock %}

{% block content %}
    <h2>{{ recipe.title }}</h2>
    <p><strong>Tavsif:</strong> {{ recipe.description }}</p>

    <p><strong>Kerakli masalliqlar:</strong></p>
    <ul>
        {% for ingredient in recipe.ingredients %}
            <li>{{ ingredient }}</li>
        {% endfor %}
    </ul>

    <p>
        Status: 
        {% if recipe.price == 0 %}
            <span class="free">Ushbu retsept mutlaqo Bepul!</span>
        {% else %}
            <span class="price">Darslik narxi: {{ recipe.price }} so'm</span>
        {% endif %}
    </p>

    <a href="/" class="back-link">&larr; Orqaga qaytish</a>
{% endblock %}"""

with open(os.path.join(TEMPLATE_DIR, 'base.html'), 'w', encoding='utf-8') as f: f.write(BASE_HTML)
with open(os.path.join(TEMPLATE_DIR, 'index.html'), 'w', encoding='utf-8') as f: f.write(INDEX_HTML)
with open(os.path.join(TEMPLATE_DIR, 'detail.html'), 'w', encoding='utf-8') as f: f.write(DETAIL_HTML)

if __name__ == '__main__':
    print("Dastur ishga tushmoqda... Brauzerda http://127.0.0.1:5000/ manziliga kiring.")
    app.run(debug=True)
