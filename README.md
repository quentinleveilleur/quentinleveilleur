<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jeu de Tir avec Pistolet Rouge</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Jeu de Tir avec Pistolet Rouge</h1>
    <canvas id="gameCanvas"></canvas>
    <script src="game.js"></script>
</body>
</html>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    background-color: #f0f0f0;
    font-family: Arial, sans-serif;
    text-align: center;
    padding-top: 20px;
}

canvas {
    border: 2px solid black;
    background-color: #fff;
    display: block;
    margin: 0 auto;
}

h1 {
    margin-bottom: 20px;
}
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = 800;
canvas.height = 600;

// Charger les images du pistolet et de la cible
const gunImage = new Image();
gunImage.src = 'gun_red.png'; // Remplacez par le chemin de votre image de pistolet rouge
const targetImage = new Image();
targetImage.src = 'target.png'; // Remplacez par le chemin de votre image de cible

let gun = { x: 50, y: canvas.height / 2 - 25, width: 50, height: 50 };
let bullets = [];
let targets = [];
let isShooting = false;

// Classe Cible
class Target {
    constructor() {
        this.x = Math.random() * (canvas.width - 50) + 200;
        this.y = Math.random() * (canvas.height - 50);
        this.width = 50;
        this.height = 50;
        this.speed = 2 + Math.random() * 2;
    }

    update() {
        this.x -= this.speed;
        if (this.x < 0) {
            this.reset();
        }
    }

    reset() {
        this.x = Math.random() * (canvas.width - 50) + 200;
        this.y = Math.random() * (canvas.height - 50);
    }

    draw() {
        ctx.drawImage(targetImage, this.x, this.y, this.width, this.height);
    }
}

// Ajouter des cibles au jeu
function addTarget() {
    targets.push(new Target());
}

// Créer une balle
function createBullet() {
    const bullet = {
        x: gun.x + gun.width,
        y: gun.y + gun.height / 2 - 5,
        width: 10,
        height: 5,
        speed: 10
    };
    bullets.push(bullet);
}

// Détection des collisions entre la balle et les cibles
function checkCollisions() {
    bullets.forEach((bullet, bulletIndex) => {
        targets.forEach((target, targetIndex) => {
            if (bullet.x < target.x + target.width &&
                bullet.x + bullet.width > target.x &&
                bullet.y < target.y + target.height &&
                bullet.y + bullet.height > target.y) {
                
                // Supprimer la balle et la cible
                bullets.splice(bulletIndex, 1);
                targets.splice(targetIndex, 1);
                addTarget(); // Ajouter une nouvelle cible
            }
        });
    });
}

// Dessiner le pistolet
function drawGun() {
    ctx.drawImage(gunImage, gun.x, gun.y, gun.width, gun.height);
}

// Dessiner les balles
function drawBullets() {
    bullets.forEach(bullet => {
        ctx.fillStyle = "red";
        ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
    });
}

// Dessiner les cibles
function drawTargets() {
    targets.forEach(target => {
        target.draw();
    });
}

// Mettre à jour le jeu
function update() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    drawGun();
    drawBullets();
    drawTargets();

    // Déplacer les balles
    bullets.forEach((bullet, index) => {
        bullet.x += bullet.speed;
        if (bullet.x > canvas.width) {
            bullets.splice(index, 1); // Supprimer la balle si elle sort de l'écran
        }
    });

    // Mettre à jour les cibles
    targets.forEach(target => target.update());

    // Vérifier les collisions
    checkCollisions();

    requestAnimationFrame(update);
}

// Mouvement du pistolet
document.addEventListener('keydown', (event) => {
    if (event.key === 'ArrowUp' && gun.y > 0) {
        gun.y -= 10;
    } else if (event.key === 'ArrowDown' && gun.y < canvas.height - gun.height) {
        gun.y += 10;
    }
});

// Tirer des balles
document.addEventListener('click', () => {
    createBullet();
});

// Ajouter initialement des cibles
for (let i = 0; i < 3; i++) {
    addTarget();
}

// Démarrer le jeu
update();
