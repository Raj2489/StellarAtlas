/src
├── /main
│   ├── /java
│   │   └── com
│   │       └── example
│   │           └── celestial
│   │               ├── CelestialApplication.java
│   │               ├── controller
│   │               │   └── CelestialController.java
│   │               └── model
│   │                   └── CelestialBody.java
│   └── /resources
│       ├── /static
│       │   ├── styles.css
│       │   └── scripts.js
│       ├── /templates
│       │   └── index.html
│       └── application.properties

package com.example.celestial;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class CelestialApplication {
    public static void main(String[] args) {
        SpringApplication.run(CelestialApplication.class, args);
    }
}

package com.example.celestial.model;

public class CelestialBody {
    private String name;
    private String description;
    private String imageUrl;
    private String quizQuestion;
    private String quizAnswer;

    // Constructors, getters, and setters
    public CelestialBody(String name, String description, String imageUrl, String quizQuestion, String quizAnswer) {
        this.name = name;
        this.description = description;
        this.imageUrl = imageUrl;
        this.quizQuestion = quizQuestion;
        this.quizAnswer = quizAnswer;
    }

    public String getName() {
        return name;
    }

    public String getDescription() {
        return description;
    }

    public String getImageUrl() {
        return imageUrl;
    }

    public String getQuizQuestion() {
        return quizQuestion;
    }

    public String getQuizAnswer() {
        return quizAnswer;
    }
}

package com.example.celestial.controller;

import com.example.celestial.model.CelestialBody;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.ArrayList;
import java.util.List;

@Controller
public class CelestialController {
    @GetMapping("/")
    public String home(Model model) {
        List<CelestialBody> celestialBodies = new ArrayList<>();
        celestialBodies.add(new CelestialBody("Proxima Centauri b",
                "Proxima Centauri b is an exoplanet orbiting the star Proxima Centauri.",
                "https://example.com/proxima.jpg",
                "What is the closest star system to Earth?",
                "Proxima Centauri"));

        // Add more celestial bodies as needed

        model.addAttribute("celestialBodies", celestialBodies);
        return "index"; // Returns index.html
    }
}

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Celestial Bodies</title>
    <link rel="stylesheet" href="/styles.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
</head>
<body>
    <header>
        <h1>Exploring Celestial Bodies</h1>
        <nav>
            <ul>
                <li><a href="#home">Home</a></li>
                <li><a href="#planets">Exoplanets</a></li>
                <li><a href="#quiz">Quiz</a></li>
                <li><a href="#about">About Us</a></li>
                <li><a href="#contact">Contact</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <section id="home">
            <h2>Discover Newly Discovered Celestial Bodies</h2>
            <div id="3d-space-container"></div>
        </section>

        <section id="planets">
            <h2>Celestial Bodies</h2>
            <div id="celestial-list">
                <ul>
                    <th:block th:each="body : ${celestialBodies}">
                        <li>
                            <h3 th:text="${body.name}"></h3>
                            <p th:text="${body.description}"></p>
                            <img th:src="${body.imageUrl}" alt="Image of celestial body" />
                            <div>
                                <h4>Quiz Question</h4>
                                <p th:text="${body.quizQuestion}"></p>
                                <input type="text" id="answer" placeholder="Your answer" />
                                <button onclick="checkAnswer('${body.quizAnswer}')">Submit</button>
                                <div id="result"></div>
                            </div>
                        </li>
                    </th:block>
                </ul>
            </div>
        </section>
    </main>

    <footer>
        <p>&copy; 2024 Celestial Explorer. All Rights Reserved.</p>
    </footer>

    <script src="/scripts.js"></script>
</body>
</html>

body, html {
    margin: 0;
    padding: 0;
    font-family: Arial, sans-serif;
    background: #000; /* Black background */
    color: #fff; /* White text */
}

header {
    background: #1a1a1a;
    padding: 15px 20px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

header h1 {
    font-size: 24px;
}

nav ul {
    list-style: none;
    display: flex;
    gap: 15px;
}

nav ul li a {
    color: #fff;
    text-decoration: none;
    font-size: 16px;
}

nav ul li a:hover {
    color: #0d98ba; /* Light blue color */
}

main {
    text-align: center;
    padding: 20px;
}

#3d-space-container {
    width: 600px;
    height: 400px;
    margin: 20px auto;
    border: 2px solid #333;
}

footer {
    background: #1a1a1a;
    padding: 10px;
    text-align: center;
}

function checkAnswer(correctAnswer) {
    const userAnswer = document.getElementById('answer').value;
    const resultDiv = document.getElementById('result');

    if (userAnswer.toLowerCase() === correctAnswer.toLowerCase()) {
        resultDiv.innerHTML = "<p style='color: green;'>Correct!</p>";
    } else {
        resultDiv.innerHTML = "<p style='color: red;'>Incorrect! The correct answer is: " + correctAnswer + "</p>";
    }
}

// Create a 3D model of a celestial body using Three.js
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(600, 400);
document.getElementById('3d-space-container').appendChild(renderer.domElement);

// Create a rotating sphere (planet)
const geometry = new THREE.SphereGeometry(1, 32, 32);
const texture = new THREE.TextureLoader().load('https://example.com/your_planet_texture.jpg'); // Sample texture
const material = new THREE.MeshStandardMaterial({ map: texture });
const planet = new THREE.Mesh(geometry, material);
scene.add(planet);

// Lighting
const ambientLight = new THREE.AmbientLight(0x404040, 2);
scene.add(ambientLight);
const directionalLight = new THREE.DirectionalLight(0xffffff, 1);
directionalLight.position.set(5, 5, 5).normalize();
scene.add(directionalLight);

// Position camera and animate
camera.position.z = 3;
function animate() {
    requestAnimationFrame(animate);
    planet.rotation.y += 0.

