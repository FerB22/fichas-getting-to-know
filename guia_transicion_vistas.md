# 🚀 Guía Práctica: Transición de Vistas Exclusivas con Animación FadeIn y FadeOut

Esta guía explica paso a paso cómo implementar un sistema de **transición de vistas exclusivas con animación de salida (fadeOut) y entrada (fadeIn)**, tal como fue aplicado en el proyecto *Forgotten Sword* y *Getting to Know*.

Este enfoque garantiza que el menú principal y la sección desplegada nunca se solapen ni queden apilados al desplazarse por la pantalla.

---

## 🛠️ Conceptos Clave

1. **Vistas Exclusivas**: Al seleccionar una opción, la vista actual se desvanece y desaparece (`display: none`), permitiendo que la nueva vista ocupe toda la pantalla limpia desde la parte superior.
2. **Animaciones CSS (@keyframes)**:
   - **`fadeIn`**: Aumenta la opacidad (`0 -> 1`) y eleva el contenido (`translateY: 20px -> 0`).
   - **`fadeOut`**: Reduce la opacidad (`1 -> 0`) y desciende el contenido (`translateY: 0 -> 20px`).
3. **Orquestación con JavaScript (`setTimeout`)**: Se aplica un temporizador para dar tiempo a que termine la animación de salida de CSS antes de cambiar el estilo a `display: none` o `display: block`.

---

## 💻 Código de Ejemplo Completo y Autocontenido

Puedes copiar y guardar este código como un archivo `.html` para probarlo inmediatamente en tu navegador:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Plantilla de Transición de Vistas</title>
    <style>
        /* 1. Habilitar desplazamiento suave */
        html {
            scroll-behavior: smooth;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            background-color: #120c08;
            color: #e8dcc4;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 40px 20px;
        }

        /* VISTA 1: MENÚ PRINCIPAL */
        .main-container {
            width: 100%;
            max-width: 600px;
            min-height: 75vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            text-align: center;
        }

        .main-container.hidden {
            display: none !important;
        }

        .main-container.closing {
            animation: fadeOut 0.3s ease-in forwards;
        }

        .main-container.fadeIn {
            animation: fadeIn 0.4s ease-out forwards;
        }

        .btn-opcion {
            width: 100%;
            padding: 18px 24px;
            margin: 10px 0;
            background: #24180e;
            color: #f5eedc;
            border: 2px solid #9e8237;
            border-radius: 10px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .btn-opcion:hover {
            background: #322214;
            border-color: #d4af37;
            transform: translateY(-3px);
        }

        /* VISTAS SECUNDARIAS (OCULTAS POR DEFECTO) */
        .seccion-oculta {
            display: none;
            width: 100%;
            max-width: 900px;
            margin: 0 auto;
            text-align: center;
            padding: 20px 0;
        }

        .seccion-oculta.active {
            display: block;
            animation: fadeIn 0.4s ease-out forwards;
        }

        .seccion-oculta.closing {
            animation: fadeOut 0.3s ease-in forwards;
        }

        .btn-cerrar {
            margin-bottom: 25px;
            padding: 10px 22px;
            background: rgba(212, 175, 55, 0.1);
            color: #d4af37;
            border: 1px solid #9e8237;
            border-radius: 20px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.25s ease;
        }

        .btn-cerrar:hover {
            background: #d4af37;
            color: #120c08;
        }

        /* 2. REGLAS DE ANIMACIÓN CSS */
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
    </style>
</head>
<body>

    <!-- MENÚ PRINCIPAL INICIAL -->
    <main class="main-container" id="menu-principal">
        <h1>Mi Proyecto Web</h1>
        <p>Selecciona una opción para abrir su contenido:</p>

        <button class="btn-opcion" onclick="mostrarSeccion('seccion-1', 'opcion1')">
            🛡️ Sección Opción 1
        </button>

        <button class="btn-opcion" onclick="mostrarSeccion('seccion-2', 'opcion2')">
            📖 Sección Opción 2
        </button>
    </main>

    <!-- VISTA SECUNDARIA 1 -->
    <section class="seccion-oculta" id="seccion-1">
        <button class="btn-cerrar" onclick="ocultarSeccion('seccion-1')">
            ← Volver al Menú Principal
        </button>
        <h2>Contenido de la Opción 1</h2>
        <p>Aquí va la información o galería de elementos...</p>
    </section>

    <!-- VISTA SECUNDARIA 2 -->
    <section class="seccion-oculta" id="seccion-2">
        <button class="btn-cerrar" onclick="ocultarSeccion('seccion-2')">
            ← Volver al Menú Principal
        </button>
        <h2>Contenido de la Opción 2</h2>
        <p>Aquí va el catálogo o lista de ítems...</p>
    </section>

    <!-- LÓGICA JAVASCRIPT DE ANIMACIÓN Y TRANSICIÓN -->
    <script>
        function mostrarSeccion(idTarget, hash) {
            const menu = document.getElementById('menu-principal');
            const target = document.getElementById(idTarget);

            // 1. Ocultar cualquier otra sección abierta previa
            ['seccion-1', 'seccion-2'].forEach(secId => {
                if (secId !== idTarget) {
                    const el = document.getElementById(secId);
                    if (el) {
                        el.classList.remove('active', 'closing');
                        el.style.display = 'none';
                    }
                }
            });

            // 2. Animar salida del menú principal (fadeOut)
            menu.classList.add('closing');

            // 3. Esperar 250ms a que concluya el desvanecimiento del menú
            setTimeout(() => {
                menu.classList.add('hidden');
                menu.classList.remove('closing');

                // 4. Mostrar la vista objetivo con animación de entrada (fadeIn)
                target.style.display = 'block';
                target.classList.remove('closing');
                target.classList.add('active');
                
                // Actualizar hash de URL y desplazar arriba
                window.location.hash = hash;
                window.scrollTo({ top: 0, behavior: 'smooth' });
            }, 250);
        }

        function ocultarSeccion(idTarget) {
            const target = document.getElementById(idTarget);
            const menu = document.getElementById('menu-principal');

            // 1. Animar salida de la vista activa (fadeOut)
            target.classList.add('closing');

            // 2. Esperar 250ms a que termine el desvanecimiento
            setTimeout(() => {
                target.classList.remove('active', 'closing');
                target.style.display = 'none';

                // 3. Reaparecer el menú principal limpiamente con fadeIn
                menu.classList.remove('hidden');
                menu.classList.add('fadeIn');

                window.location.hash = '';
                window.scrollTo({ top: 0, behavior: 'smooth' });

                // Limpiar la clase auxiliar fadeIn tras finalizar la entrada
                setTimeout(() => {
                    menu.classList.remove('fadeIn');
                }, 400);
            }, 250);
        }

        // Auto-abrir sección si la URL contiene #opcion1 o #opcion2
        window.addEventListener('DOMContentLoaded', () => {
            if (window.location.hash === '#opcion1') {
                mostrarSeccion('seccion-1', 'opcion1');
            } else if (window.location.hash === '#opcion2') {
                mostrarSeccion('seccion-2', 'opcion2');
            }
        });
    </script>

</body>
</html>
```

---

## 🔬 Análisis Detallado del Funcionamiento

### ¿Por qué se necesita `setTimeout`?
Si remueves `display: block` inmediatamente al hacer clic en un botón, el navegador oculta el elemento en **0 milisegundos**, impidiendo que la animación de salida CSS se ejecute.

Con la estructura:
1. JavaScript agrega la clase `.closing`.
2. CSS ejecuta `@keyframes fadeOut` durante `0.3s`.
3. `setTimeout` espera **250ms** y recién ahí cambia `display` a `none` y oculta la vista.

---

## 📌 Pasos para Replicarlo en Otros Proyectos

1. **Define tus vistas separadas**: Mantén el contenedor del menú inicial y los contenedores de contenido en etiquetas `<main>` o `<section>` independientes.
2. **Copia las animaciones `@keyframes`**: Añade `fadeIn` y `fadeOut` a tus estilos CSS.
3. **Copia la lógica JavaScript**: Asigna `mostrarSeccion()` a los botones de entrada y `ocultarSeccion()` al botón de retorno.
