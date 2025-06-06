# Evolución del Diseño: De Modelo del Dominio a Diseño Orientado a Objetos

Este documento describe la evolución progresiva del diseño de un sistema de gestión de partidos de fútbol, desde el análisis inicial hasta la implementación de principios de orientación a objetos.

## Tabla de Contenidos
1. [Modelo del Dominio (MDD)](#1-modelo-del-dominio-mdd)
2. [Diseño Básico](#2-diseño-básico)
3. [Diseño Modular](#3-diseño-modular)
4. [Diseño Orientado a Objetos](#4-diseño-orientado-a-objetos)
5. [Conclusiones](#5-conclusiones)

---

## 1. Modelo del Dominio (MDD)

### Características
- **Objetivo**: Identificar las entidades principales del mundo real
- **Enfoque**: Capturar conceptos fundamentales y sus relaciones básicas
- **Archivo**: `modeloDelDominio.puml`

### Entidades Identificadas
- Partido (de fútbol)
- Encuentro (de fútbol)
- Los árbitros
- El equipo
- Un jugador / Otro jugador
- Las reglas de juego
- Períodos
- Alineación

### Fortalezas
- Captura correctamente las entidades del mundo real
- Identifica relaciones fundamentales entre conceptos

### Áreas de Mejora Detectadas
- Mezcla niveles de abstracción ("las reglas de juego" vs entidades concretas)
- Ambigüedad en la diferencia entre "Partido" y "Encuentro"
- Falta de cardinalidades explícitas
- "Períodos" aparece sin contexto claro

---

## 2. Diseño Básico

### Características
- **Objetivo**: Refinar el modelo conceptual hacia un diseño técnico
- **Enfoque**: Definir responsabilidades, atributos y comportamientos
- **Archivos**: `diseño.puml`, `diseñoCompleto.puml`

### Mejoras Implementadas

#### Jerarquía Clara
```
abstract class Persona
├── Arbitro
└── Jugador
```

#### Responsabilidades Definidas
- **Partido**: `iniciar()`, `finalizar()`, `registrarGol()`
- **Arbitro**: `dirigirPartido()`, `mostrarTarjeta()`, `validarJugada()`
- **Jugador**: `jugar()`, `marcarGol()`, `recibirTarjeta()`

#### Atributos Específicos
- **Jugador**: `dorsal`, `posicion`, `activo`
- **Arbitro**: `licencia`, `categoria`, `experiencia`
- **Equipo**: `nombre`, `entrenador`, `ciudad`

#### Relaciones Refinadas
- Cardinalidades más precisas
- Direccionalidades clarificadas
- Dependencias explícitas (árbitro valida reglas, jugador las respeta)

### Diferenciación Conceptual
- **Partido**: Instancia específica de juego
- **Encuentro**: Contexto más amplio que puede contener múltiples partidos

---

## 3. Diseño Modular

### Características
- **Objetivo**: Organizar el sistema en módulos cohesivos
- **Enfoque**: Separación de responsabilidades por dominios funcionales
- **Archivos**: Dos versiones evolutivas

### Versión v0: Modularización Básica

#### Packages Definidos
1. **Entidades Base**: `Persona`, `Encuentro`, `Partido`
2. **Gestión de Tiempo**: `GestorTiempo`, `Periodos`
3. **Gestión de Equipos**: `Equipo`, `Jugador`, `GestorAlineacion`
4. **Arbitraje**: `Arbitro`, `Reglas`
5. **Gestión de Eventos**: `GestorEventos`, `Resultado`

#### Innovaciones Clave
- **Patrón Manager/Controller**: Introducción de gestores especializados
- **Cohesión funcional**: Cada package con responsabilidad específica
- **Eliminación de redundancias**: `Alineacion` reemplazada por `GestorAlineacion`

### Versión v1: Arquitectura Multicapa

#### Evolución Adicional
- **Package Presentación**: Separación de lógica de interfaz
  - `DashboardVista`
  - `EncuentroVista`
  - `PartidoVista`
  - `EstadisticasVista`
- **Funcionalidad Analítica**: Clase `Estadisticas` para métricas
- **Patrón MVC Implícito**: Vista → Modelo a través de gestores

#### Ventajas de v1
- Preparado para múltiples interfaces (web, móvil)
- Escalabilidad mejorada
- Separación clara entre dominio y presentación

---

## 4. Diseño Orientado a Objetos

### Características
- **Objetivo**: Aplicar principios OO para máxima flexibilidad y mantenibilidad
- **Enfoque**: Abstracción, polimorfismo, encapsulación y composición
- **Archivos**: `diseñoOO-v0.puml`, `diseñoOOCompleto-v0.puml`

### Principios OO Aplicados

#### 1. Abstracción e Interfaces
```
interface IControlTiempo
interface IGestorFormacion  
interface IValidadorReglas
interface IRegistroEventos
interface ICalculadorEstadisticas
```

**Beneficios**:
- Contratos bien definidos
- Ocultación de implementación
- Bajo acoplamiento

#### 2. Polimorfismo Estratégico

##### Sistema de Reglas Extensible
```
abstract class ReglasDeportivas implements IValidadorReglas
├── ReglasFutbol
└── ReglasBasket
```

##### Jerarquía de Vistas
```
abstract class VistaBase
├── DashboardVista
├── EncuentroVista
├── PartidoVista
└── EstadisticasVista
```

#### 3. Encapsulación Avanzada
- **Inyección de dependencias**: 
  - `Partido.setReglasPartido(IValidadorReglas)`
  - `Arbitro.setValidadorReglas(IValidadorReglas)`
- **Atributos protegidos**: `#deporte`, `#version` en `ReglasDeportivas`

#### 4. Composición sobre Herencia
- **Patrón Composite**: `DashboardVista` compone `List<VistaBase>`
- **Relaciones flexibles**: A través de interfaces en lugar de herencia rígida

### Capacidades Arquitectónicas

#### Extensibilidad
- Nuevos deportes: Solo implementar `IValidadorReglas`
- Nuevas interfaces: Sin afectar lógica de negocio
- Nuevos tipos de eventos: Extensión natural del sistema

#### Testabilidad
- Interfaces facilitan creación de mocks
- Inyección de dependencias permite testing aislado
- Cada componente es testeable independientemente

#### Mantenibilidad
- Cambios localizados por responsabilidad única
- Bajo acoplamiento entre módulos
- Alta cohesión dentro de cada package

---

## 5. Conclusiones

### Progresión Metodológica

| Etapa | Enfoque | Valor Agregado |
|-------|---------|----------------|
| **MDD** | Análisis conceptual | Identificación de entidades del dominio |
| **Diseño Básico** | Refinamiento técnico | Responsabilidades y comportamientos definidos |
| **Diseño Modular** | Organización funcional | Separación de responsabilidades por dominio |
| **Diseño OO** | Flexibilidad arquitectónica | Extensibilidad, testabilidad y mantenibilidad |

### Lecciones Aprendidas

1. **Evolución Incremental**: Cada etapa construye sobre la anterior sin perder coherencia
2. **Refinamiento Progresivo**: De conceptos vagos a implementaciones concretas
3. **Separación de Responsabilidades**: Progresión natural hacia arquitectura limpia
4. **Flexibilidad Creciente**: Cada etapa aumenta la capacidad de adaptación del sistema

### Resultado Final

El diseño final representa un sistema:
- **Robusto**: Maneja múltiples escenarios y deportes
- **Extensible**: Fácil agregar nuevas funcionalidades
- **Mantenible**: Cambios localizados y controlados
- **Testeable**: Componentes independientes y mockeable

Esta progresión demuestra cómo un análisis sistemático puede evolucionar desde conceptos básicos hasta una arquitectura de software madura y profesional.