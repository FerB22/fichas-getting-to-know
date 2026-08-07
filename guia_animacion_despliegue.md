# 🚀 Guía Práctica: Animación de Entrada y Salida con Desplazamiento Suave (Smooth Scroll)

Esta guía explica paso a paso cómo implementar las animaciones de **apertura (fadeIn)** y **cierre (fadeOut)** con desplazamiento suave, aplicadas en el botón **"Fichas de Personajes"** y **"Volver al Menú Principal"**, para que puedas replicarlas en cualquier proyecto web.

---

## 🛠️ Conceptos Clave

1. **Entrada (Aparición + Deslizamiento Hacia Arriba)**: Al hacer clic en abrir, la sección pasa a `display: block` y ejecuta `@keyframes fadeIn` (`opacity: 0 -> 1`, `translateY: 20px -> 0`).
2. **Salida (Desvanecimiento + Deslizamiento Hacia Abajo)**: Al presionar "Volver al Menú", se añade la clase `.closing` (`@keyframes fadeOut`), se desplaza suavemente la pantalla hacia el menú principal y tras finalizar la animación (350ms con `setTimeout`), la sección vuelve a `display: none`.

---

## 💻 Plantilla de Código Completa

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ejemplo de Animación Entrada y Salida</title>
    <style>
        /* 1. Habilitar desplazamiento suave */
        html {
            scroll-behavior: smooth;
        }

        body {
            font-family: Arial, sans-serif;
            margin: 0;
            background-color: #f7faf7;
            color: #1b4332;
        }

        .seccion-principal {
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
        }

        .btn-abrir {
            padding: 16px 28px;
            background-color: #2d6a4f;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: bold;
            cursor: pointer;
            transition: transform 0.2s ease, background-color 0.2s ease;
        }

        .btn-abrir:hover {
            background-color: #1b4332;
            transform: translateY(-2px);
        }

        /* 2. Sección oculta por defecto */
        .seccion-oculta {
            display: none;
            max-width: 800px;
            margin: 0 auto;
            padding: 40px 20px;
        }

        /* 3. Animación de ENTRADA */
        .seccion-oculta.active {
            display: block;
            animation: fadeIn 0.4s ease-out forwards;
        }

        /* 4. Animación de SALIDA */
        .seccion-oculta.closing {
            animation: fadeOut 0.35s ease-in forwards;
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: translateY(20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        @keyframes fadeOut {
            from {
                opacity: 1;
                transform: translateY(0);
            }
            to {
                opacity: 0;
                transform: translateY(20px);
            }
        }

        .btn-cerrar {
            margin-bottom: 20px;
            padding: 8px 18px;
            background-color: #e8f5e9;
            border: 1px solid #2d6a4f;
            border-radius: 20px;
            cursor: pointer;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <!-- MENÚ / SECCIÓN INICIAL -->
    <main class="seccion-principal" id="menu-principal">
        <h1>Mi Proyecto Web</h1>
        <button class="btn-abrir" onclick="mostrarSeccion()">
            🛡️ Fichas de Personajes
        </button>
    </main>

    <!-- SECCIÓN REVELADA CON ANIMACIONES DE ENTRADA Y SALIDA -->
    <section class="seccion-oculta" id="seccion-contenido">
        <button class="btn-cerrar" onclick="ocultarSeccion()">
            ← Volver al Menú Principal
        </button>
        <h2>Fichas de Personajes</h2>
        <p>Contenido detallado de las fichas...</p>
    </section>

    <!-- LÓGICA JAVASCRIPT -->
    <script>
        function mostrarSeccion() {
            const seccion = document.getElementById('seccion-contenido');
            
            // Remover animación de cierre previa si existía
            seccion.classList.remove('closing');
            
            // Activar visibilidad y animación de entrada (fadeIn)
            seccion.classList.add('active');
            
            // Actualizar URL
            window.location.hash = 'fichas';
            
            // Desplazar suavemente a la sección
            seccion.scrollIntoView({ behavior: 'smooth' });
        }

        function ocultarSeccion() {
            const seccion = document.getElementById('seccion-contenido');
            
            // Activar la animación de salida (fadeOut)
            seccion.classList.add('closing');
            
            // Iniciar desplazamiento suave de regreso al menú arriba
            document.getElementById('menu-principal').scrollIntoView({ behavior: 'smooth' });
            
            // Esperar los 350ms que dura fadeOut antes de remover las clases y ocultar
            setTimeout(() => {
                seccion.classList.remove('active', 'closing');
                window.location.hash = '';
            }, 350);
        }

        // Auto-abrir si la URL contiene #fichas
        window.addEventListener('DOMContentLoaded', () => {
            if (window.location.hash === '#fichas') {
                mostrarSeccion();
            }
        });
    </script>
</body>
</html>
```

---

## 🔍 ¿Por Qué Se Usa `setTimeout` al Volver al Menú?

Si se remueve `display: block` inmediatamente al hacer clic en "Volver", el navegador ocultaría la sección en 0 milisegundos y **la animación de salida nunca se vería**.

Al usar `setTimeout(..., 350)`:
1. JavaScript le añade la clase `.closing` a la sección.
2. CSS ejecuta `@keyframes fadeOut` durante 0.35s (350 milisegundos).
3. Mientras se desvanece, la pantalla se desplaza suavemente hacia arriba (`scrollIntoView`).
4. Tras transcurrir los 350ms, JavaScript limpia las clases y oculta completamente el contenedor.
