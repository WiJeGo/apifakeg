### Documentación del Modelo de Datos - Sistema de Método Alemán

## 1. Entidad: USUARIO

**Descripción de la entidad**: Almacena la información de los usuarios que acceden al sistema para gestionar y analizar bonos corporativos utilizando el método alemán de amortización. Cada usuario puede crear y gestionar múltiples bonos.

| Atributo | Tipo de Dato | Descripción
|-----|-----|-----
| id | INT | Identificador único del usuario. Clave primaria auto-incremental que identifica de manera única a cada usuario en el sistema.
| nombre | VARCHAR(100) | Nombre del usuario. Campo obligatorio que almacena el nombre de pila del usuario.
| apellido | VARCHAR(100) | Apellido del usuario. Campo obligatorio que almacena los apellidos del usuario.
| email | VARCHAR(150) | Correo electrónico del usuario. Campo único y obligatorio que sirve como identificador para el inicio de sesión.
| password_hash | VARCHAR(255) | Contraseña encriptada del usuario. Almacena la contraseña del usuario en formato hash por motivos de seguridad.
| activo | BIT | Estado del usuario. Indica si la cuenta del usuario está activa (1) o inactiva (0). Por defecto es 1.


**Función en el sistema**: La entidad USUARIO es fundamental para el control de acceso y la seguridad del sistema. Permite la autenticación de usuarios y asocia las operaciones realizadas con un usuario específico, facilitando la auditoría y el seguimiento de actividades.

---

## 2. Entidad: EMPRESA

**Descripción de la entidad**: Registra la información de las empresas emisoras de bonos corporativos. Cada empresa puede emitir múltiples bonos que serán analizados mediante el método alemán.

| Atributo | Tipo de Dato | Descripción
|-----|-----|-----
| id | INT | Identificador único de la empresa. Clave primaria auto-incremental que identifica de manera única a cada empresa en el sistema.
| nombre | VARCHAR(200) | Nombre de la empresa. Campo obligatorio que almacena la razón social o nombre comercial de la empresa emisora.
| ruc | VARCHAR(20) | RUC o identificador fiscal. Campo único y obligatorio que almacena el número de identificación tributaria de la empresa.
| sector | VARCHAR(100) | Sector económico. Indica el sector o industria a la que pertenece la empresa (ej. Tecnología, Finanzas, Energía, etc.).


**Función en el sistema**: La entidad EMPRESA permite categorizar y organizar los bonos según su emisor. Esto facilita el análisis comparativo de bonos emitidos por la misma empresa o por empresas del mismo sector, proporcionando contexto adicional para la toma de decisiones de inversión.

---

## 3. Entidad: BONO

**Descripción de la entidad**: Almacena la información detallada de los bonos corporativos que serán analizados mediante el método alemán de amortización. Cada bono está asociado a una empresa emisora y a un usuario que lo registra en el sistema.

| Atributo | Tipo de Dato | Descripción
|-----|-----|-----
| id | INT | Identificador único del bono. Clave primaria auto-incremental que identifica de manera única a cada bono en el sistema.
| empresa_id | INT | Identificador de la empresa emisora. Clave foránea que relaciona el bono con la empresa que lo emite.
| usuario_id | INT | Identificador del usuario. Clave foránea que relaciona el bono con el usuario que lo registró en el sistema.
| valor_nominal | DECIMAL(15,2) | Valor nominal del bono. Monto total del bono al momento de su emisión, expresado en la moneda correspondiente.
| tasa_interes | DECIMAL(8,4) | Tasa de interés efectiva. Porcentaje de interés que se aplicará para calcular los pagos periódicos (expresado en decimal, ej. 0.05 para 5%).
| plazo_meses | INT | Plazo total en meses. Duración total del bono desde su emisión hasta su vencimiento, expresado en meses.
| frecuencia_pago | VARCHAR(20) | Frecuencia de pago. Indica la periodicidad de los pagos (MENSUAL, TRIMESTRAL, SEMESTRAL, ANUAL).
| fecha_emision | DATE | Fecha de emisión del bono. Fecha en la que se emite el bono y comienza a generar intereses.
| comision | DECIMAL(8,4) | Comisión aplicada. Porcentaje de comisión cobrada por la emisión o colocación del bono (expresado en decimal).
| descuento | DECIMAL(8,4) | Descuento aplicado. Porcentaje de descuento aplicado al valor nominal al momento de la emisión (expresado en decimal).
| periodo_gracia | INT | Período de gracia en meses. Número de meses iniciales durante los cuales solo se pagan intereses sin amortización de capital.


**Función en el sistema**: La entidad BONO es el núcleo del sistema, ya que contiene todos los parámetros necesarios para aplicar el método alemán de amortización. Estos parámetros determinan cómo se calcularán los pagos periódicos, los intereses y la amortización del capital a lo largo del tiempo.

---

## 4. Entidad: AMORTIZACION

**Descripción de la entidad**: Registra cada uno de los períodos de pago de un bono, calculados según el método alemán de amortización. Cada registro representa un pago periódico con su correspondiente desglose de intereses y amortización de capital.

| Atributo | Tipo de Dato | Descripción
|-----|-----|-----
| id | INT | Identificador único del registro de amortización. Clave primaria auto-incremental.
| bono_id | INT | Identificador del bono. Clave foránea que relaciona el registro de amortización con el bono correspondiente.
| numero_periodo | INT | Número de período. Indica el número secuencial del período de pago dentro del plazo total del bono.
| fecha_pago | DATE | Fecha programada de pago. Fecha en la que debe realizarse el pago correspondiente a este período.
| saldo_inicial | DECIMAL(15,2) | Saldo inicial del período. Monto pendiente de pago al inicio del período actual.
| interes | DECIMAL(15,2) | Interés del período. Monto correspondiente a los intereses generados durante el período, calculado sobre el saldo inicial.
| amortizacion | DECIMAL(15,2) | Cuota de amortización. Monto destinado a reducir el capital (constante en el método alemán).
| cuota_total | DECIMAL(15,2) | Cuota total. Suma del interés y la amortización, representa el pago total del período.
| saldo_final | DECIMAL(15,2) | Saldo final. Monto pendiente de pago después de aplicar la amortización del período actual.


**Función en el sistema**: La entidad AMORTIZACION implementa el método alemán, donde la amortización del capital es constante en cada período, mientras que los intereses disminuyen progresivamente a medida que se reduce el saldo pendiente. Esta tabla permite visualizar la evolución de los pagos a lo largo del tiempo, mostrando cómo las cuotas totales van disminuyendo gradualmente.

---

## Relaciones entre Entidades

1. **USUARIO - BONO**: Relación uno a muchos (1:N). Un usuario puede crear y gestionar múltiples bonos, pero cada bono está asociado a un único usuario.
2. **EMPRESA - BONO**: Relación uno a muchos (1:N). Una empresa puede emitir múltiples bonos, pero cada bono está asociado a una única empresa emisora.
3. **BONO - AMORTIZACION**: Relación uno a muchos (1:N). Un bono tiene múltiples registros de amortización (uno por cada período de pago), pero cada registro de amortización pertenece a un único bono.


## Características del Método Alemán

El método alemán de amortización, implementado en este modelo de datos, se caracteriza por:

- **Amortización constante**: La cuota de amortización del capital es la misma en todos los períodos (valor_nominal / número_de_períodos).
- **Intereses decrecientes**: Los intereses se calculan sobre el saldo pendiente, que disminuye con cada pago.
- **Cuotas decrecientes**: A diferencia de otros métodos como el francés, las cuotas totales (amortización + interés) disminuyen progresivamente.
- **Período de gracia**: Permite configurar un período inicial durante el cual solo se pagan intereses, sin amortización de capital.


Este modelo de datos proporciona la estructura necesaria para implementar el método alemán de amortización y facilitar el análisis de bonos corporativos como instrumentos de inversión.
