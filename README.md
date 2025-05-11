# Modelo de Base de Datos - Método Alemán

## 1. Entidades

### 1.1. USUARIO

Almacena la información de los usuarios que acceden al sistema para gestionar y analizar bonos corporativos utilizando el método alemán de amortización. Cada usuario puede crear y gestionar múltiples bonos.

| Atributo | Tipo de Dato | Explicación |
|----------|--------------|-------------|
| id | INT IDENTITY(1,1) | Identificador único autoincremental que sirve como clave primaria de la tabla. |
| nombre_completo | VARCHAR(100) | Nombre completo del usuario que utilizará el sistema. No puede ser nulo. |
| email | VARCHAR(150) | Correo electrónico del usuario, utilizado para la autenticación. Debe ser único y no puede ser nulo. |
| password_hash | VARCHAR(255) | Almacena el hash de la contraseña del usuario para garantizar la seguridad. No puede ser nulo. |
| activo | BIT | Indica si la cuenta del usuario está activa (1) o inactiva (0). Por defecto es 1. |
| fecha_registro | DATETIME | Fecha y hora en que el usuario se registró en el sistema. Por defecto es la fecha actual. |

### 1.2. BONO

Almacena la información detallada de los bonos corporativos que serán analizados mediante el método alemán de amortización. Cada bono está asociado a un usuario que lo registra en el sistema.

| Atributo | Tipo de Dato | Explicación |
|----------|--------------|-------------|
| id | INT IDENTITY(1,1) | Identificador único autoincremental que sirve como clave primaria de la tabla. |
| usuario_id | INT | Identificador del usuario que creó el bono. Es una clave foránea que referencia a la tabla usuario. |
| valor_nominal | DECIMAL(15,2) | Monto que el emisor se compromete a devolver al vencimiento del bono. No puede ser nulo. |
| plazo_anios | DECIMAL(5,2) | Tiempo total durante el cual el bono estará vigente, expresado en años. No puede ser nulo. |
| frecuencia_cupon | INT | Número de pagos de interés (cupones) que se realizan al año. No puede ser nulo. |
| tipo_tasa_interes | VARCHAR(20) | Especifica si la tasa de interés es Efectiva o Nominal. Por defecto es 'Nominal'. |
| capitalizacion_dias | INT | Frecuencia con la que los intereses se capitalizan, expresada en días. Solo aplica si el tipo de tasa es Nominal. Por defecto es 1 (diaria). |
| tasa_interes | DECIMAL(8,4) | Porcentaje que el emisor paga periódicamente como interés sobre el valor nominal. No puede ser nulo. |
| tasa_anual_descuento | DECIMAL(8,4) | Tasa utilizada para descontar los flujos futuros y calcular el valor presente del bono. No puede ser nulo. |
| fecha_emision | DATE | Fecha en que se emite el bono. No puede ser nulo. |
| tipo_periodo_gracia | VARCHAR(20) | Especifica si el periodo de gracia es Total (no se paga nada) o Parcial (se pagan solo intereses). Por defecto es 'Total'. |
| num_periodos_gracia | INT | Número de periodos iniciales durante los cuales no se realizan pagos de capital o intereses, según el tipo de periodo de gracia. Por defecto es 0. |
| nombre_proyecto | VARCHAR(200) | Nombre opcional para identificar el proyecto o propósito del bono. |

### 1.3. AMORTIZACION

Registra el detalle de cada periodo de pago del bono, incluyendo los montos de interés, amortización y saldos. Implementa el método alemán donde la amortización es constante y los intereses disminuyen con el tiempo.

| Atributo | Tipo de Dato | Explicación |
|----------|--------------|-------------|
| id | INT IDENTITY(1,1) | Identificador único autoincremental que sirve como clave primaria de la tabla. |
| bono_id | INT | Identificador del bono al que pertenece este registro de amortización. Es una clave foránea que referencia a la tabla bono. |
| numero_periodo | INT | Número secuencial que identifica el periodo de pago. No puede ser nulo. |
| fecha_pago | DATE | Fecha en que se debe realizar el pago correspondiente a este periodo. No puede ser nulo. |
| saldo_inicial | DECIMAL(15,2) | Saldo de la deuda al inicio del periodo. No puede ser nulo. |
| interes | DECIMAL(15,2) | Monto de interés a pagar en este periodo, calculado sobre el saldo inicial. No puede ser nulo. |
| amortizacion | DECIMAL(15,2) | Monto de capital que se amortiza en este periodo. En el método alemán, este valor es constante. No puede ser nulo. |
| cuota_total | DECIMAL(15,2) | Suma del interés y la amortización, representa el pago total del periodo. No puede ser nulo. |
| saldo_final | DECIMAL(15,2) | Saldo de la deuda al finalizar el periodo, después de aplicar la amortización. No puede ser nulo. |

### 1.4. RESULTADO_BONO

Almacena los resultados de los análisis financieros realizados sobre el bono, incluyendo métricas importantes como duración, convexidad y tasas efectivas.

| Atributo | Tipo de Dato | Explicación |
|----------|--------------|-------------|
| id | INT IDENTITY(1,1) | Identificador único autoincremental que sirve como clave primaria de la tabla. |
| bono_id | INT | Identificador del bono al que pertenecen estos resultados. Es una clave foránea que referencia a la tabla bono. |
| duracion | DECIMAL(10,4) | Duración de Macaulay, que representa el promedio ponderado del tiempo en que se reciben los flujos de caja del bono. No puede ser nulo. |
| convexidad | DECIMAL(10,4) | Medida que refleja cómo cambia la duración del bono ante variaciones en la tasa de interés. No puede ser nulo. |
| duracion_modificada | DECIMAL(10,4) | Estima el cambio porcentual en el precio del bono ante una variación de 1% en la tasa de interés. No puede ser nulo. |
| tcea_emisor | DECIMAL(8,4) | Tasa de Costo Efectiva Anual para el emisor del bono. No puede ser nulo. |
| trea_bonista | DECIMAL(8,4) | Tasa de Rendimiento Efectivo Anual esperada por el inversor/bonista. No puede ser nulo. |
| fecha_calculo | DATETIME | Fecha y hora en que se realizó el cálculo de estos resultados. Por defecto es la fecha actual. |

## 2. Relaciones entre Entidades

### 2.1. USUARIO - BONO

**Tipo de relación:** Uno a muchos (1:N)

**Descripción:** Un usuario puede crear y gestionar múltiples bonos, pero cada bono está asociado a un único usuario. Esta relación se implementa mediante la clave foránea `usuario_id` en la tabla `bono`, que referencia al campo `id` de la tabla `usuario`.

**Propósito:** Esta relación permite:
- Mantener un registro de quién creó cada bono
- Permitir a los usuarios acceder solo a sus propios bonos
- Facilitar la gestión de permisos y acceso a la información

### 2.2. BONO - AMORTIZACION

**Tipo de relación:** Uno a muchos (1:N)

**Descripción:** Un bono tiene múltiples registros de amortización (uno por cada período de pago), pero cada registro de amortización pertenece a un único bono. Esta relación se implementa mediante la clave foránea `bono_id` en la tabla `amortizacion`, que referencia al campo `id` de la tabla `bono`.

**Propósito:** Esta relación permite:
- Calcular y almacenar el cronograma de pagos completo de un bono
- Visualizar cómo se distribuyen los pagos de interés y capital a lo largo del tiempo
- Implementar el método alemán de amortización, donde la amortización es constante y los intereses disminuyen con el tiempo

### 2.3. BONO - RESULTADO_BONO

**Tipo de relación:** Uno a muchos (1:N)

**Descripción:** Un bono puede tener múltiples registros de resultados (por ejemplo, si se realizan análisis en diferentes momentos o con diferentes parámetros), pero cada registro de resultados pertenece a un único bono. Esta relación se implementa mediante la clave foránea `bono_id` en la tabla `resultado_bono`, que referencia al campo `id` de la tabla `bono`.

**Propósito:** Esta relación permite:
- Almacenar los resultados de los análisis financieros realizados sobre el bono
- Mantener un historial de análisis si se realizan cálculos en diferentes momentos
- Facilitar la comparación de diferentes escenarios o condiciones de mercado
```

