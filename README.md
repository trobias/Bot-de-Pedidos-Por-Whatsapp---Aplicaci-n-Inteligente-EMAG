# Bot-de-Pedidos-Por-Whatsapp--Aplicacion-Inteligente-EMAG
Automatización completa de pedidos mayoristas de refrigerantes y agua destilada, usando n8n, Postgres, Evolution API y Docker.
Este bot replica la lógica de un carrito de e-commerce, pero directamente en WhatsApp, permitiendo a los clientes: seleccionar productos, editar cantidades, eliminar, vaciar carrito, ingresar dirección y confirmar su pedido.

🛠️ Tecnologías y dependencias

Docker Compose – orquestación de servicios.

Postgres 16 – base de datos para almacenar borradores de pedidos.

n8n 1.110.1 – motor de automatización del flujo.

Evolution API – gateway de WhatsApp para enviar/recibir mensajes.

Node.js (n8n) – nodos Code para lógica en JavaScript.

LLM opcional (Gemini / OpenAI) – usado solo como router de intenciones.

📂 Ejemplo de stack en docker-compose.yml:

n8n

postgres

evolution-api

⚙️ Instalación

Clonar este repositorio.

Configurar variables de entorno para Postgres y Evolution API.

Levantar los servicios con:

docker compose up -d


Importar el workflow desde My workflow 2 (19).json en n8n.

🔄 Flujo del bot (workflow)

Webhook Evolution API → recibe mensajes de WhatsApp.

Parse Message → extrae:

chat_id

texto

selectedRowId y selectedDescription (si viene de catálogo/lista)

ubicación GPS (latitude, longitude).

Detect Pedido → determina acción (insert, update, summary, address, update_specific, delete_one, etc.).

Postgres → guarda y actualiza en la tabla orders_draft.

Build Summary → genera resumen del pedido con totales e importe.

Switch de acciones → decide siguiente paso:

mostrar catálogo

editar cantidad

eliminar producto

vaciar carrito

confirmar pedido con dirección

pasar a humano.

SendText / SendList (Evolution API) → responde al cliente.

📦 Lógica del pedido (CRUD)

Este bot implementa todas las operaciones de un carrito de compras:

Create (INSERT) → cliente selecciona producto del catálogo.

Read (READ) → cliente pide ver catálogo o resumen de pedido.

Update (UPDATE) → cliente envía nueva cantidad o edita un producto específico.

Delete (DELETE) → cliente elimina un producto o vacía el carrito.

Todo se gestiona en la tabla orders_draft con columnas:
id, chat_id, product_code, product_label, quantity, status, address, pending_edit_row.

✨ Características principales

Gestión de pedidos vía WhatsApp sin necesidad de apps externas.

Selección guiada de productos (catálogo interactivo).

Control granular del carrito:

Agregar productos.

Editar cantidades.

Eliminar productos específicos.

Vaciar carrito completo.

Reiniciar carrito (reset por chat_id).

Confirmación obligatoria con dirección:

Texto libre (ej: “Av. Siempreviva 742”).

Coordenadas GPS (ej: -27.3748, -55.9006).

Transferencia a humano (distribuidor):

Automática tras confirmar dirección.

Cliente puede escribir volver o esperar 30 min para regresar al bot.

Manejo de borradores (draft):

Persistencia del carrito aunque el cliente interrumpa la conversación.

Resúmenes dinámicos:

Calcula subtotales, total de ítems y total de importe.

Soporte de lenguaje natural y números:

“Quiero 10 de amarillo” o “diez de azul”.

🚀 Ventajas competitivas

Experiencia simple para el cliente: solo WhatsApp, sin apps externas.

Automatización total del ciclo de compra hasta que entra un humano.

Escalable y multiusuario: cada chat_id mantiene su propio borrador.

Carrito flexible: productos editables y eliminables en cualquier momento.

Robustez de entradas: acepta tanto texto como GPS.

Bajo consumo de IA:

El 90% de la lógica es determinística (JS + SQL).

El LLM se usa solo como router de intenciones, reduciendo costo y tokens.

Extensible:

Fácil de integrar con ERP, logística o pasarelas de pago.

🛒 Equivalente a un e-commerce clásico

Este bot replica funciones tradicionales de un carrito online:

Función e-commerce	Función en WhatsApp Bot
Carrito (draft)	Tabla orders_draft por chat_id
Checkout (confirmación)	Confirmación con dirección obligatoria
CRUD del carrito	Insert / Read / Update / Delete en n8n + SQL
Persistencia	Pedido se guarda aunque el chat se interrumpa
Atención al cliente	Derivación a humano tras dirección
📚 Futuras mejoras

Integrar pagos automáticos.

Validación de stock en tiempo real.

Historial de pedidos por cliente.

Panel de administración para distribuidores.

📷 Workflow visual


(incluye switches, nodos SQL, IA router y lógica de catálogo/resumen)

📝 Licencia

Este proyecto se distribuye bajo licencia MIT.
