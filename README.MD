# Auto-Evaluación Docente Script 🎓

Script de automatización para el formulario de evaluación docente. Permite completar rápidamente las encuestas con diferentes perfiles de satisfacción (Crítico, Normal o Excelente) utilizando un banco de respuestas aleatorias para evitar patrones repetitivos.

## 🚀 Instrucciones de Uso

1. **Abrir el formulario:** Ingresa a la página de la evaluación docente que deseas completar.
2. **Abrir la Consola de Desarrollador:**
   - Presiona `F12` o `Ctrl + Shift + I` (Cmd + Option + I en Mac).
   - Haz clic derecho en cualquier parte de la página y selecciona **Inspeccionar**.
3. **Habilitar el pegado (Si es necesario):**
   Si la consola bloquea el pegado de código, escribe lo siguiente y presiona Enter:
   ```javascript
   allow pasting
   ```
4. **Pegar el código:** Copia el script completo proporcionado abajo, pégalo en la consola y presiona **Enter**.

---

## ⚙️ Configuración

Puedes personalizar el comportamiento del script modificando las primeras líneas del código:

| Variable | Descripción | Valores permitidos |
| :--- | :--- | :--- |
| `nivelDeSatisfaccion` | Define el tono de la evaluación | `0` (Crítico), `1` (Medio/Bueno), `2` (Excelente) |
---

## 🛠️ Script (Actualización 21/04/2026)

```javascript
(async function() {
    const URL_DATA = "https://raw.githubusercontent.com/TETvega/DippFormUNAH/main/DATA.JSON";
    const nivelDeSatisfaccion = 2; // 0: CRITICA, 1: INTERMEDIO, 2: EXCELENTE

    try {
        const response = await fetch(URL_DATA);
        const DATA = await response.json();
        const config = DATA[nivelDeSatisfaccion];
        
        const random = (l) => l[Math.floor(Math.random() * l.length)];
        const nombreDocente = document.querySelector('td[colspan="3"]')?.innerText.trim() || "EL DOCENTE";

        console.log(`%c Iniciando Auto-Evaluación: ${nombreDocente}`);

        // 1. Procesar Selects (Puntuación)
        document.querySelectorAll('select').forEach((s) => {
            s.value = nivelDeSatisfaccion === 0 ? "1" : (nivelDeSatisfaccion === 1 ? "3" : "4");
            const id = s.id.replace(/inputSelect|Select/, '');
            if (window.FuncionGuardar) FuncionGuardar(id, s, '');
        });

        // 2. Procesar Textareas (Comentarios)
        document.querySelectorAll('textarea').forEach((t) => {
            const match = t.getAttribute('onblur')?.match(/FuncionGuardar2\((\d+)/);
            if (match) {
                const id = match[1];
                let r = id === "280" ? random(config.fortalezas) : 
                        id === "281" ? random(config.mejoras) : 
                        id === "282" ? random(config.quejas) : 
                        id === "283" ? nombreDocente : "";
                
                t.value = r;
                if (window.FuncionGuardar2) FuncionGuardar2(parseInt(id), 0, r);
            }
        });

        console.log("Proceso completado exitosamente.");
    } catch (error) {
        console.error("Error al obtener el banco DATA de GitHub:", error);
    }
})();
```

## ⚠️ Nota
Este script es para uso educativo. Asegúrate de revisar las respuestas antes de enviar el formulario para confirmar que reflejan tu opinión real.
