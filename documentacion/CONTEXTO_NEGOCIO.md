# Contexto de Negocio — SofkianOS MVP

## 1. Descripción del Proyecto
*   **Nombre del Proyecto:** SofkianOS (Sistema Operativo de Kudos)
*   **Objetivo del Proyecto:** Automatizar y fortalecer la cultura de reconocimiento y recompensas dentro de Sofka, permitiendo que los colaboradores envíen "Kudos" (reconocimientos tangibles) a sus compañeros para celebrar logros y contribuciones, promoviendo la integración en equipos distribuidos.

## 2. Flujos Críticos del Negocio
*   **Principales Flujos de Trabajo:**
    *   **Envío de Kudo:** El empleado selecciona un remitente, un destinatario, una categoría y escribe un mensaje de reconocimiento.
    *   **Validación y Publicación:** El sistema valida que los datos sean correctos y que no sea un auto-reconocimiento, aceptando la solicitud de inmediato (asincronía).
    *   **Procesamiento y Gamificación:** Un proceso en segundo plano calcula los puntos asociados al Kudo según su categoría y lo registra para la trazabilidad y futuras recompensas.
*   **Módulos o Funcionalidades Críticas:**
    *   **Módulo de Emisión (Producer API):** Punto de entrada que garantiza que el reconocimiento sea válido y se encole correctamente.
    *   **Módulo de Procesamiento (Consumer Worker):** Cerebro del sistema que aplica las reglas de puntos y persiste la información.
    *   **Interfaz de Usuario (Frontend):** Experiencia de usuario premium para facilitar el envío de reconocimientos con feedback visual inmediato.

## 3. Reglas de Negocio y Restricciones
*   **Reglas de Negocio Relevantes:**
    *   **Invariante de Auto-Kudo:** Un empleado no puede enviarse un Kudo a sí mismo.
    *   **Escala de Puntos:** Los puntos se asignan según la categoría del valor Sofkiano (ej. TEAMWORK: 10 pts, INNOVATION: 15 pts, EXCELLENCE: 20 pts).
    *   **Campos Obligatorios:** Todo Kudo debe tener un mensaje de al menos 10 caracteres para ser válido.
*   **Regulaciones o Normativas:**
    *   **Protección de Datos:** Manejo de identidades de colaboradores internos.
    *   **Trazabilidad:** Registro exacto de quién, para quién y cuándo se emitió cada reconocimiento.

## 4. Perfiles de Usuario y Roles
*   **Perfiles o Roles de Usuario en el Sistema:**
    *   **Empleado Sofka:** Usuario final que puede enviar y recibir reconocimientos.
    *   **Administrador (Roadmap):** Gestor encargado de configurar categorías y revisar excepciones o errores en el procesamiento.
*   **Permisos y Limitaciones de Cada Perfil:**
    *   El **Empleado** solo interactúa con el formulario de envío y la visualización de su impacto. No tiene acceso a la configuración de la escala de puntos ni a la gestión de la infraestructura.

## 5. Condiciones del Entorno Técnico
*   **Plataformas Soportadas:**
    *   Aplicación Web responsiva accesible desde navegadores modernos.
*   **Tecnologías o Integraciones Clave:**
    *   **Backend:** Java 17 con Spring Boot bajo arquitectura hexagonal.
    *   **Frontend:** React 19 con TypeScript y Vite.
    *   **Mensajería:** RabbitMQ para asegurar que ningún kudo se pierda y el sistema sea altamente escalable.
    *   **Infraestructura:** Despliegue automatizado en AWS EC2 mediante Docker.

## 6. Casos Especiales o Excepciones
*   **Escenarios Alternos o Excepciones que Deben Considerarse:**
    *   **Falla en el Procesamiento:** Si un Kudo no puede ser procesado (ej. error de base de datos), el sistema lo mueve automáticamente a una "Dead Letter Queue" (kudos.dlq) para que no se pierda y pueda ser procesado manualmente después.
    *   **Indisponibilidad del Broker:** Si el sistema de mensajería falla, el Producer API debe informar al usuario que el servicio no está disponible temporalmente.
