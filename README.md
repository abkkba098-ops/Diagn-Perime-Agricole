/* * FICHIER: auth.js
 * Gestion de l'authentification et de la sécurité
 */

// Configuration des utilisateurs autorisés
const USERS = [
    {
        role: 'admin',
        email: 'abkkba098@gmail.com',
        pass: 'abkkba77800763',
        name: 'Administrateur Principal'
    },
    {
        role: 'guest',
        email: 'admin', // Nom d'utilisateur simple demandé
        pass: '1234567',
        name: 'Invité Ministère'
    }
];

// Fonction de connexion
function login(event) {
    event.preventDefault(); // Empêche le rechargement de la page

    const emailInput = document.getElementById('email').value;
    const passInput = document.getElementById('password').value;
    const errorMsg = document.getElementById('error-message');

    // Vérification des identifiants
    const user = USERS.find(u => u.email === emailInput && u.pass === passInput);

    if (user) {
        // Succès : On enregistre la session
        sessionStorage.setItem('isLoggedIn', 'true');
        sessionStorage.setItem('userName', user.name);
        sessionStorage.setItem('userRole', user.role);
        
        // Redirection vers le tableau de bord
        window.location.href = 'index.html';
    } else {
        // Erreur
        errorMsg.style.display = 'block';
        errorMsg.innerText = "Identifiants incorrects. Veuillez réessayer.";
        
        // Animation de vibration pour l'erreur
        const loginBox = document.querySelector('.login-box');
        loginBox.classList.add('shake');
        setTimeout(() => loginBox.classList.remove('shake'), 500);
    }
}

// Fonction de déconnexion
function logout() {
    sessionStorage.clear();
    window.location.href = 'login.html';
}

// Vérification de sécurité (à mettre sur index.html)
function checkSecurity() {
    const loggedIn = sessionStorage.getItem('isLoggedIn');
    if (!loggedIn) {
        // Si pas connecté, renvoyer au login
        window.location.href = 'login.html';
    } else {
        // Afficher le nom de l'utilisateur
        console.log("Connecté en tant que : " + sessionStorage.getItem('userName'));
    }
}
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Connexion | Diagnostics Agricoles Djibouti</title>
    <link rel="stylesheet" href="styles.css">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        /* Styles spécifiques à la page de connexion */
        body.login-page {
            background: linear-gradient(135deg, var(--primary-color) 0%, #2c3e50 100%);
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .login-box {
            background: white;
            padding: 2.5rem;
            border-radius: 15px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.2);
            width: 100%;
            max-width: 400px;
            text-align: center;
        }

        .login-header { margin-bottom: 2rem; }
        .login-header i { color: var(--primary-color); margin-bottom: 1rem; }
        .login-header h2 { color: var(--secondary-color); margin: 0; }
        .login-header p { color: #7f8c8d; font-size: 0.9rem; margin-top: 5px; }

        .input-group {
            position: relative;
            margin-bottom: 1.5rem;
            text-align: left;
        }

        .input-group i {
            position: absolute;
            left: 15px;
            top: 50%;
            transform: translateY(-50%);
            color: #bdc3c7;
        }

        .input-group input {
            width: 100%;
            padding: 12px 15px 12px 40px;
            border: 1px solid #dfe6e9;
            border-radius: 8px;
            box-sizing: border-box; /* Important pour ne pas dépasser */
            font-size: 1rem;
            transition: border-color 0.3s;
        }

        .input-group input:focus {
            border-color: var(--primary-color);
            outline: none;
        }

        .btn-login {
            width: 100%;
            padding: 12px;
            background: var(--primary-color);
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.3s;
        }

        .btn-login:hover { background: #219150; }

        .error-message {
            color: #e74c3c;
            font-size: 0.9rem;
            margin-bottom: 1rem;
            display: none;
            background: #fadbd8;
            padding: 10px;
            border-radius: 5px;
        }

        /* Animation en cas d'erreur */
        .shake { animation: shake 0.5s; }
        @keyframes shake {
            0% { transform: translateX(0); }
            25% { transform: translateX(-10px); }
            50% { transform: translateX(10px); }
            75% { transform: translateX(-10px); }
            100% { transform: translateX(0); }
        }
    </style>
</head>
<body class="login-page">

    <div class="login-box">
        <div class="login-header">
            <i class="fas fa-leaf fa-3x"></i>
            <h2>Espace Sécurisé</h2>
            <p>Ministère de l'Agriculture - Djibouti</p>
        </div>

        <div id="error-message" class="error-message"></div>

        <form onsubmit="login(event)">
            <div class="input-group">
                <i class="fas fa-user"></i>
                <input type="text" id="email" placeholder="Email ou Identifiant" required>
            </div>
            
            <div class="input-group">
                <i class="fas fa-lock"></i>
                <input type="password" id="password" placeholder="Mot de passe" required>
            </div>

            <button type="submit" class="btn-login">
                Se Connecter <i class="fas fa-arrow-right"></i>
            </button>
        </form>
    </div>

    <script src="auth.js"></script>
</body>
</html>
