Aquí tienes el documento técnico en formato Markdown que define la especificación de este nuevo lenguaje. Es una mezcla de la seguridad de **Zig**, el tipado de **Go** y la elegancia sintáctica de **Lua**.

Podemos llamarlo **Lugo** (Lua + Go).

---

# Especificación del Lenguaje: Lugo

**Lugo** es un lenguaje de programación de sistemas diseñado para ser intuitivo, seguro y de alto rendimiento. Combina la semántica de valor (copia por defecto) con una sintaxis limpia inspirada en Lua y un sistema de tipos estático.

## 1. Filosofía de Diseño

- **Intuición primero:** La asignación entre variables siempre crea una copia independiente (Semántica de Valor).
    
- **Sin Punteros Explícitos:** No existe la aritmética de punteros ni los símbolos `*` o `&`.
    
- **Seguridad de Memoria:** Arrays dinámicos con verificación de límites (_Bound Checking_) nativa.
    
- **Mutabilidad Explícita:** Todo es constante (`const`) por defecto a menos que se declare como variable (`var`).
    

---

## 2. Declaración de Variables

Lugo utiliza una sintaxis de declaración clara donde el tipo se define después del nombre.

```Lua
-- Inmutable (Copia de seguridad)
const pi: float = 3.14159
const nombre = "Luzi" -- Inferencia de tipo automática

-- Mutable (Puede cambiar su propio valor)
var contador: int = 0
contador = 10 -- Permitido
```

### El concepto de Copia en Asignación

Al asignar una variable a otra, se crea una copia profunda. Los cambios en una no afectan a la otra.

```Lua
var lista_a = {1, 2, 3}
var lista_b = lista_a -- lista_b es una COPIA física de lista_a

lista_a.add(4)
-- lista_a es {1, 2, 3, 4}
-- lista_b sigue siendo {1, 2, 3}
```

---

## 3. Estructuras de Datos

### Structs

Las estructuras definen tipos de datos personalizados. No tienen métodos ocultos; son contenedores de datos puros.

```Lua
type Player struct
    Nombre: string
    Salud:  int
    Equipamiento: []string
end
```

### Arrays Dinámicos

Los arrays en Lugo son dinámicos por defecto y crecen según sea necesario. El acceso fuera de rango produce un error en tiempo de ejecución, evitando vulnerabilidades de memoria.


```Lua
var inventario: []string = {"Espada", "Escudo"}
inventario.add("Poción") -- El array crece automáticamente
```

---

## 4. Funciones y Control de Flujo

### Sintaxis de Funciones

Las funciones utilizan palabras clave de Lua (`then`, `do`, `end`) pero mantienen el tipado estático de Go.


```Lua
fun calcular_daño(base: int, critico: bool): int
    if critico then
        return base * 2
    else
        return base
    end
end
```

### Paso de Parámetros: Copia vs Referencia

Por defecto, los parámetros se pasan por **copia** para evitar efectos secundarios. Si se desea modificar el objeto original, se debe usar la palabra clave `var` en el parámetro.



```Lua
-- 'p' es una copia. Los cambios aquí no afectan al original.
fun simular(p: Player)
    p.Salud = 0 
end
-- 'const p' indica que recibimos el objeto original solo lectura (Referencia).
fun simular(const p: Player)
    p.Salud = 0 
end
-- 'var p' indica que recibimos el objeto original (Referencia).
fun curar(var p: Player, cantidad: int)
    p.Salud = p.Salud + cantidad
end
```

---

## 5. Ejemplo Completo


```lua
type Enemigo struct
    Tipo: string
    HP:   int
end

fun recibir_ataque(var e: Enemigo, daño: int)
    e.HP = e.HP - daño
    if e.HP <= 0 then
        print(e.Tipo .. " ha sido derrotado")
    end
end

fun main()
    -- Creamos una instancia mutable
    var orco = Enemigo{ Tipo = "Orco", HP = 50 }
    
    const daño_espada = 30
    
    -- El orco se modifica directamente porque la función usa 'var'
    recibir_ataque(orco, daño_espada)
    
    print("Vida restante: ", orco.HP)
end
```

---

## 6. Gestión de Memoria

Lugo utiliza un sistema de **conteo de referencias optimizado** con **Copy-on-Write (COW)**.

- Las copias de grandes estructuras o arrays no ocurren físicamente hasta que una de las variables intenta modificar los datos.
    
- Esto permite tener la **facilidad de las copias** con la **velocidad de los punteros**.
Esa es una excelente observación. Si `type` solo sirviera para estructuras, se sentiría como un "ruido" innecesario que podrías reemplazar directamente por la palabra `struct`.

Sin embargo, para que **Lugo** sea un lenguaje de sistemas completo y coherente, el keyword `type` tiene que ser el motor para definir la identidad de los datos. Siguiendo la lógica de Go y Zig, `type` debería usarse para **Aliasing** (dar nombres nuevos a tipos existentes) y para **Enums**, lo que permite que el código sea mucho más semántico.

Aquí te muestro cómo expandiríamos el uso de `type` para que sea realmente útil:

---

### 1. Alias de Tipos (Single Types)

Esto es vital para la legibilidad. En lugar de usar siempre `int`, puedes crear un tipo que explique _qué representa_ ese número. Esto ayuda al compilador a evitar que sumes "peras con manzanas".



```Lua
-- Definimos tipos específicos basados en tipos base
type ID: int
type Kilometros: float
type Segundos: int

fun calcular_velocidad(distancia: Kilometros, tiempo: Segundos): float
    return distancia / tiempo
end

fun main()
    const mi_id: ID = 505
    const d: Kilometros = 100.5
    const t: Segundos = 3600
    
    -- El código es auto-documentado
    const v = calcular_velocidad(d, t)
end
```

### 2. Definición de Enums (Tipos Enumerados)

En lugar de usar números mágicos o strings, usamos `type` para definir un set cerrado de opciones. Esto es mucho más seguro y eficiente.


```Lua
type EstadoMotor enum
    Apagado
    Encendido
    Falla
end

fun revisar_motor(e: EstadoMotor)
    if e == EstadoMotor.Falla then
        print("¡Alerta de sistema!")
    end
end
```

### 3. Tipos de Función (Callbacks)

A veces quieres pasar una función como si fuera una variable. `type` permite definir la "firma" de esa función para que el compilador verifique que sea la correcta.

```Lua
-- Definimos un tipo que es "una función que recibe un int y no devuelve nada"
type LoggerFunc: func(msg: string)

fun ejecutar_con_log(log: LoggerFunc)
    log("Ejecutando tarea...")
end
```

---

### ¿Por qué mantener `type` y no usar solo `struct`?

Si solo tuviéramos `struct`, el lenguaje sería muy limitado. Al tener `type`, ganamos tres cosas:

1. **Abstracción:** Puedes cambiar el tipo subyacente (de `int` a `float64`) en un solo lugar (`type Moneda: int`) y todo el programa se actualiza.
    
2. **Seguridad de Tipos:** El compilador puede avisarte si intentas asignar un `ID` a una variable de `Kilometros`, aunque ambos sean `int` por debajo.
    
3. **Consistencia:** Toda "definición de algo nuevo" empieza con la misma palabra. Es más fácil para el parser del lenguaje y para tu memoria visual.
    

| **Uso**         | **Ejemplo**                    |
| --------------- | ------------------------------ |
| **Estructuras** | `type Usuario struct ... end`  |
| **Alias**       | `type Metros: float`           |
| **Enums**       | `type Color enum ... end`      |
| **Funciones**   | `type Callback: fun() -> bool` |

**¿Qué te parece?** Con esto, `type` deja de ser un "decorador de structs" para convertirse en la herramienta con la que diseñas el dominio de tu aplicación.

¿Crees que deberíamos permitir que un `type` tenga métodos (como en Go) o preferirías mantener las funciones separadas para que sea más simple?

Aquí tienes el resumen de las decisiones de diseño para **Luzy**, estructurado para que sirva como referencia técnica para tu implementación en **Zig**.

---

# 1. Especificaciones de Diseño: Lenguaje Luzy (v0.1)

Luzy es un lenguaje de scripting de alto rendimiento diseñado para motores de videojuegos, implementado en **Zig**, con un enfoque en la **legibilidad absoluta**, **semántica de copia** y **seguridad de tipos** sin punteros visibles.

---

## 2. Sistema de Variables y Mutabilidad

La mutabilidad y el tipo de dato se infieren o se declaran explícitamente. Se fomenta el uso de convenciones de nomenclatura para la intención del programador.

- **`var`**: Declara una variable mutable (Recomendado: `lower_snake_case`).
    
- **`const`**: Declara una constante inmutable (Recomendado: `UPPER_SNAKE_CASE`).
    
- **Inferencia**: Si no se usa keyword, el _case_ determina la mutabilidad.
    

### Parámetros en Funciones

|**Sintaxis**|**Comportamiento**|
|---|---|
|`const a: int`|Solo lectura (Inmutable).|
|`var b: int`|Referencia mutable (Los cambios afectan al exterior).|
|`c: int`|Semántica de copia (Cualquier cambio es local).|

---

## 3. Tipado Fuerte y Coerción Segura

Luzy evita errores de precisión silenciosos.

- **Sin pérdida de datos**: No se permite asignar un `float` a un `int` si hay decimales significativos.
    
- **Promoción automática**: Un `int` se promociona a `float` en operaciones mixtas (ej. `1 + 2.5 = 3.5`).
    
- **División**: El operador `/` siempre devuelve un `float`. Para división entera, se usan `//` o `div`.
    

---

## 4. Gestión de Nulidad y Errores (Inspirado en Zig)

Se eliminan los punteros nulos y las excepciones. Se usan contenedores explícitos.

### Tipos Especiales

- **Opcionales (`?`)**: `Entity?` puede contener una entidad o estar vacío (`null` / `empty`).
    
- **Unión de Error (`!`)**: `File!` puede ser un archivo válido o un error (`error` / `broken`).
    
- **Combinados (`?!`)**: Puede fallar técnicamente O no existir.
    

### Keywords de Estado

Para maximizar la legibilidad, el Lexer mapea múltiples términos al mismo estado lógico:

|**Estado**|**Keywords Permitidas**|**Significado**|
|---|---|---|
|**Nulidad**|`null`, `empty`|El valor opcional está vacío.|
|**Fallo**|`error`, `broken`|La variable contiene un fallo técnico.|

---

## 5. Operadores y Gramática

El lenguaje permite redundancia sintáctica para adaptarse al estilo del usuario (técnico o prosa).

### Igualdad y Comparación

- **`is` == `==`**: Son gemelos idénticos en la gramática. Se usan tanto para comparar valores como para checar estados (`empty`, `broken`).
    
- **`isnt` == `!=` == `~=`**: Sinónimos para la desigualdad.
    

### Lógica y Negación

- **Negación Lógica**: Se usan `not` o `~`.
    
- **Signo `!`**: Reservado **exclusivamente** para el sistema de errores (tipado y unwrap), no para la negación booleana.
    

### Manejo de Flujo (Smart Casting)

No se requiere "renombrar" variables (ej. `if target as e`). El compilador realiza un análisis de flujo:

Lua

```
if target is not empty then
    -- Dentro de este bloque, 'target' es tratado como el tipo base (sin ?)
    target.draw() 
end
```

---

## 6. Estructuras de Datos

- **Arrays**: Funcionan por **semántica de copia**.
    
    Lua
    
    ```
    arr_a = {1, 2, 3}
    arr_b = arr_a      -- Copia física de los datos
    arr_a.add(4)       -- Solo afecta a arr_a
    ```
    
- **EECS/DOD**: Diseñado para procesar datos en bloques contiguos de memoria gestionados por los _Allocators_ de Zig del motor anfitrión.
    

---

## 7. Notas de Implementación (Lexer/Parser)

- **Unificación de Tokens**: El Lexer debe emitir el mismo `TokenID` para sinónimos (ej. `is` y `==`).
    
- **Sin Sobrecarga Gramatical**: La gramática solo conoce una regla de "Igualdad". La distinción entre comparar un `int` o un `empty` la resuelve el **Type Checker**.
    
- **Keywords Reservadas**: `null`, `empty`, `error`, `broken` son literales del sistema.
    

---