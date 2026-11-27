STEP 5 — Set up index.html
File location: GCA - Grand City Authority/index.html (the file in the main project folder, NOT the one inside src).
1. In VS Code, click index.html.
2. Delete everything inside it.
3. Paste this complete code:

"<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="theme-color" content="#101820">
    <title>GCA — Grand City Authority</title>
</head>
<body>
    <div id="game">
        <div id="loading-screen">
            <h1>GCA</h1>
            <p>GRAND CITY AUTHORITY</p>
            <span>INITIALIZING CITY...</span>
        </div>
    </div>

    <script type="module" src="/src/main.js"></script>
</body>
</html>"



4. Press Command + S to save.

STEP 6 — Set up style.css
File location: GCA - Grand City Authority/src/style.css
1. In VS Code, open the src folder.
2. Click style.css.
3. Delete anything inside it.
4. Paste this complete code:


"* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

html,
body {
    width: 100%;
    height: 100%;
    overflow: hidden;
    background: #101820;
    font-family: Arial, sans-serif;
}

#game {
    position: relative;
    width: 100%;
    height: 100%;
    overflow: hidden;
}

canvas {
    display: block;
    width: 100%;
    height: 100%;
}

#loading-screen {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 12px;
    color: white;
    background: #101820;
    z-index: 10;
}

#loading-screen h1 {
    font-size: 76px;
    letter-spacing: 12px;
}

#loading-screen p {
    font-size: 14px;
    letter-spacing: 5px;
    color: #aab8c2;
}

#loading-screen span {
    margin-top: 35px;
    font-size: 11px;
    letter-spacing: 3px;
    color: #65c8a3;
}"

5. Press Command + S to save.

STEP 7 — Create the main game engine
File location: GCA - Grand City Authority/src/main.js
1. Open the src folder in VS Code.
2. Click main.js.
3. Delete anything inside it.
4. Paste this complete code:


"import * as THREE from 'three';
import './style.css';

// =====================================
// GCA — GRAND CITY AUTHORITY
// Prototype 1.0
// =====================================

// Main game container
const game = document.getElementById('game');

// Create the 3D scene
const scene = new THREE.Scene();

scene.background = new THREE.Color(0x9bbbd0);
scene.fog = new THREE.Fog(0x9bbbd0, 100, 450);

// Create the camera
const camera = new THREE.PerspectiveCamera(
    65,
    window.innerWidth / window.innerHeight,
    0.1,
    1000
);

camera.position.set(0, 12, 25);
camera.lookAt(0, 0, 0);

// Create the renderer
const renderer = new THREE.WebGLRenderer({
    antialias: true,
    powerPreference: 'high-performance'
});

renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5));
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

game.appendChild(renderer.domElement);

// =====================================
// LIGHTING
// =====================================

const skyLight = new THREE.HemisphereLight(
    0xc9e6ff,
    0x606050,
    2
);

scene.add(skyLight);

const sunlight = new THREE.DirectionalLight(0xffe0b5, 3);

sunlight.position.set(60, 100, 40);
sunlight.castShadow = true;
sunlight.shadow.mapSize.set(1024, 1024);

scene.add(sunlight);

// =====================================
// GROUND
// =====================================

const groundGeometry = new THREE.PlaneGeometry(500, 500);

const groundMaterial = new THREE.MeshStandardMaterial({
    color: 0x566b4e,
    roughness: 1
});

const ground = new THREE.Mesh(groundGeometry, groundMaterial);

ground.rotation.x = -Math.PI / 2;
ground.receiveShadow = true;

scene.add(ground);

// =====================================
// ROAD
// =====================================

const roadGeometry = new THREE.BoxGeometry(16, 0.12, 300);

const roadMaterial = new THREE.MeshStandardMaterial({
    color: 0x292d32,
    roughness: 0.95
});

const road = new THREE.Mesh(roadGeometry, roadMaterial);

road.position.y = 0.06;
road.receiveShadow = true;

scene.add(road);

// Road markings
const lineMaterial = new THREE.MeshBasicMaterial({
    color: 0xe8e0c0
});

for (let z = -140; z < 150; z += 12) {
    const line = new THREE.Mesh(
        new THREE.BoxGeometry(0.18, 0.03, 5),
        lineMaterial
    );

    line.position.set(0, 0.14, z);
    scene.add(line);
}

// =====================================
// BUILDINGS
// =====================================

function createBuilding(x, z, width, height, depth, color) {
    const geometry = new THREE.BoxGeometry(
        width,
        height,
        depth
    );

    const material = new THREE.MeshStandardMaterial({
        color,
        roughness: 0.8
    });

    const building = new THREE.Mesh(geometry, material);

    building.position.set(
        x,
        height / 2,
        z
    );

    building.castShadow = true;
    building.receiveShadow = true;

    scene.add(building);
}

// Building colors
const buildingColors = [
    0x8d969b,
    0xb0a18d,
    0x777f87,
    0x9caaa9,
    0x77716b,
    0xb4b0a5
];

// Create buildings along both sides of the road
for (let i = 0; i < 22; i++) {
    const z = -130 + i * 12;

    const leftHeight = 12 + Math.random() * 35;
    const rightHeight = 15 + Math.random() * 45;

    createBuilding(
        -18 - Math.random() * 12,
        z,
        10,
        leftHeight,
        10,
        buildingColors[i % buildingColors.length]
    );

    createBuilding(
        18 + Math.random() * 12,
        z,
        10,
        rightHeight,
        10,
        buildingColors[(i + 2) % buildingColors.length]
    );
}

// =====================================
// ANIMATION
// =====================================

function animate() {
    requestAnimationFrame(animate);

    renderer.render(scene, camera);
}

animate();

// =====================================
// WINDOW RESIZING
// =====================================

window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight;

    camera.updateProjectionMatrix();

    renderer.setSize(
        window.innerWidth,
        window.innerHeight
    );

    renderer.setPixelRatio(
        Math.min(window.devicePixelRatio, 1.5)
    );
});

// Hide loading screen
document.getElementById('loading-screen').style.display = 'none';

console.log('GCA Prototype 1.0 initialized.');

antialias: true,
    powerPreference: 'high-performance'
});

renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5));
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

game.appendChild(renderer.domElement);

// =====================================
// LIGHTING
// =====================================

const skyLight = new THREE.HemisphereLight(
    0xc9e6ff,
    0x606050,
    2
);

scene.add(skyLight);

const sunlight = new THREE.DirectionalLight(0xffe0b5, 3);

sunlight.position.set(60, 100, 40);
sunlight.castShadow = true;
sunlight.shadow.mapSize.set(1024, 1024);

scene.add(sunlight);

// =====================================
// GROUND
// =====================================

const groundGeometry = new THREE.PlaneGeometry(500, 500);

const groundMaterial = new THREE.MeshStandardMaterial({
    color: 0x566b4e,
    roughness: 1
});

const ground = new THREE.Mesh(groundGeometry, groundMaterial);

ground.rotation.x = -Math.PI / 2;
ground.receiveShadow = true;

scene.add(ground);

// =====================================
// ROAD
// =====================================

const roadGeometry = new THREE.BoxGeometry(16, 0.12, 300);

const roadMaterial = new THREE.MeshStandardMaterial({
    color: 0x292d32,
    roughness: 0.95
});

const road = new THREE.Mesh(roadGeometry, roadMaterial);

road.position.y = 0.06;
road.receiveShadow = true;

scene.add(road);

// Road markings
const lineMaterial = new THREE.MeshBasicMaterial({
    color: 0xe8e0c0
});

for (let z = -140; z < 150; z += 12) {
    const line = new THREE.Mesh(
        new THREE.BoxGeometry(0.18, 0.03, 5),
        lineMaterial
    );

    line.position.set(0, 0.14, z);
    scene.add(line);
}

// =====================================
// BUILDINGS
// =====================================

function createBuilding(x, z, width, height, depth, color) {
    const geometry = new THREE.BoxGeometry(
        width,
        height,
        depth
    );

    const material = new THREE.MeshStandardMaterial({
        color,
        roughness: 0.8
    });

    const building = new THREE.Mesh(geometry, material);

    building.position.set(
        x,
        height / 2,
        z
    );

    building.castShadow = true;
    building.receiveShadow = true;

    scene.add(building);
}

// Building colors
const buildingColors = [
    0x8d969b,
    0xb0a18d,
    0x777f87,
    0x9caaa9,
    0x77716b,
    0xb4b0a5
];

// Create buildings along both sides of the road
for (let i = 0; i < 22; i++) {
    const z = -130 + i * 12;

    const leftHeight = 12 + Math.random() * 35;
    const rightHeight = 15 + Math.random() * 45;

    createBuilding(
        -18 - Math.random() * 12,
        z,
        10,
        leftHeight,
        10,
        buildingColors[i % buildingColors.length]
    );

    createBuilding(
        18 + Math.random() * 12,
        z,
        10,
        rightHeight,
        10,
        buildingColors[(i + 2) % buildingColors.length]
    );
}

// =====================================
// ANIMATION
// =====================================

function animate() {
    requestAnimationFrame(animate);

    renderer.render(scene, camera);
}

animate();

// =====================================
// WINDOW RESIZING
// =====================================

window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight;

    camera.updateProjectionMatrix();

    renderer.setSize(
        window.innerWidth,
        window.innerHeight
    );

    renderer.setPixelRatio(
        Math.min(window.devicePixelRatio, 1.5)
    );
});

// Hide loading screen
document.getElementById('loading-screen').style.display = 'none';

console.log('GCA Prototype 1.0 initialized.');"

5. Press Command + S.
