# Biblia de Flujos - Fundación La Caixa
## Sistema de Gestión de Incidencias para Centros de Personas Mayores

**Versión:** 1.0
**Fecha:** Enero 2026
**Cliente:** Fundación La Caixa
**Sector:** Centros residenciales de personas mayores

---

## 1. Visión General del Sistema

### 1.1 Problema que Resuelve

El sistema gestiona las incidencias de mantenimiento en los **centros de personas mayores** de Fundación La Caixa. Permite reportar averías y problemas, asignar proveedores para su resolución, gestionar presupuestos y dar seguimiento hasta el cierre completo de cada incidencia.

### 1.2 Flujo Principal

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  INCIDENCIA │────▶│  ASIGNACIÓN │────▶│ PRESUPUESTO │────▶│  RESOLUCIÓN │
│   CREADA    │     │  PROVEEDOR  │     │  (opcional) │     │   TÉCNICA   │
└─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
                                                                   │
                                                                   ▼
                                              ┌─────────────┐ ┌─────────────┐
                                              │  VALORACIÓN │─▶│   CIERRE    │
                                              │  ECONÓMICA  │ │             │
                                              └─────────────┘ └─────────────┘
```

### 1.3 Contexto del Negocio

- **Tipo de instalaciones:** Centros residenciales de personas mayores (residencias, centros de día)
- **Estructura organizativa:** Múltiples centros gestionados por instituciones cliente
- **Modelo de trabajo:** Los proveedores externos realizan el mantenimiento
- **Supervisión:** Control central que coordina y aprueba todas las actuaciones

---

## 2. Usuarios y Roles

### 2.1 Tipos de Usuarios

| Rol | Descripción | Responsabilidad principal |
|-----|-------------|---------------------------|
| **Control** | Administrador central del sistema | Coordina todo, asigna proveedores, aprueba presupuestos y resoluciones |
| **Gestor** | Responsable de uno o varios centros | Reporta incidencias, da seguimiento a las de sus centros |
| **Cliente** | Personal del centro (directores, técnicos internos) | Reporta incidencias, consulta estado |
| **Proveedor** | Empresa de mantenimiento externa | Ejecuta trabajos, envía presupuestos, documenta resoluciones |

### 2.2 Permisos por Rol

| Acción | Control | Gestor | Cliente | Proveedor |
|--------|---------|--------|---------|-----------|
| Crear incidencias | ✅ | ✅ | ✅ | ❌ |
| Ver todas las incidencias | ✅ | Solo sus centros | Solo sus centros | Solo asignadas |
| Asignar proveedor | ✅ | ❌ | ❌ | ❌ |
| Enviar presupuesto | ❌ | ❌ | ❌ | ✅ |
| Aprobar presupuesto | ✅ | ❌ | ❌ | ❌ |
| Resolver incidencia | ❌ | ❌ | ❌ | ✅ |
| Cerrar incidencia | ✅ | ❌ | ❌ | ❌ |
| Anular asignación | ✅ | ❌ | ❌ | ❌ |
| Poner en espera | ✅ | ❌ | ❌ | ❌ |

### 2.3 Control Territorial

- **Control:** Ve todas las incidencias de todos los centros
- **Gestor:** Ve solo las incidencias de los centros que tiene asignados
- **Cliente:** Ve solo las incidencias de su institución (centro)
- **Proveedor:** Ve solo las incidencias que le han sido asignadas

**Asignación de centros:**
- Un Gestor puede tener asignados múltiples centros
- Un Cliente pertenece a una única institución
- Un Proveedor puede trabajar para múltiples centros (según le asignen incidencias)

---

## 3. Flujo de Incidencias

### 3.1 Creación de Incidencia

**¿Quién puede crear?** Control, Gestor y Cliente

**Datos obligatorios al crear:**
- Centro/institución donde ocurre
- Descripción del problema
- Ubicación dentro del centro (opcional pero recomendado)
- Fotografías del problema (opcional pero recomendado)

**Datos opcionales:**
- Prioridad (Urgente, Crítico, Normal)
- Categoría del problema
- Documentos adjuntos

### 3.2 Estados de la Incidencia (Vista Cliente)

La incidencia tiene **dos vistas de estado**: una simplificada para el cliente y una detallada para el proveedor.

#### Estados Cliente (6 estados)

```
    ┌────────────┐
    │  ABIERTA   │ ◀── Incidencia recién creada
    └─────┬──────┘
          │
          ▼
┌─────────────────┐
│   EN ESPERA     │ ◀── Pausada temporalmente (pendiente de algo)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ EN TRAMITACIÓN  │ ◀── Proveedor asignado, trabajando
└────────┬────────┘
         │
         ▼
    ┌────────────┐
    │  RESUELTA  │ ◀── Proveedor terminó el trabajo
    └─────┬──────┘
          │
          ▼
    ┌────────────┐
    │  CERRADA   │ ◀── Control aprobó y cerró
    └────────────┘

        ↓ (En cualquier momento)
    ┌────────────┐
    │  ANULADA   │ ◀── Incidencia cancelada (no procede)
    └────────────┘
```

#### Descripción de Estados Cliente

| Estado | Significado | Quién lo pone |
|--------|-------------|---------------|
| **Abierta** | Incidencia nueva, sin atender | Automático al crear |
| **En espera** | Pausada temporalmente | Control |
| **En tramitación** | Hay proveedor trabajando en ella | Automático al asignar proveedor |
| **Resuelta** | El proveedor completó el trabajo | Automático cuando proveedor resuelve |
| **Cerrada** | Control validó y cerró | Control |
| **Anulada** | Cancelada, no se va a hacer | Control |

### 3.3 Estados del Proveedor (Vista Proveedor)

La vista del proveedor es más detallada porque incluye el proceso de presupuesto y resolución.

#### Estados Proveedor (10 estados)

```
    ┌────────────┐
    │  ABIERTA   │ ◀── Proveedor recién asignado
    └─────┬──────┘
          │
          ▼
┌─────────────────┐
│ EN RESOLUCIÓN   │ ◀── Proveedor está trabajando sin presupuesto
└────────┬────────┘
         │
         ▼
    ┌────────────┐
    │  OFERTADA  │ ◀── Proveedor envió presupuesto
    └─────┬──────┘
          │
    ┌─────┴─────┐
    │           │
    ▼           ▼
┌─────────┐  ┌──────────────────┐
│OFERTA   │  │ OFERTA A REVISAR │ ◀── Control pide modificaciones
│APROBADA │  └────────┬─────────┘
└────┬────┘           │
     │                └─────────▶ (Vuelve a OFERTADA)
     │
     ▼
┌────────────┐
│  RESUELTA  │ ◀── Proveedor completó la resolución técnica
└─────┬──────┘
      │
      ▼
┌────────────┐
│  VALORADA  │ ◀── Proveedor completó valoración económica
└─────┬──────┘
      │
┌─────┴─────┐
│           │
▼           ▼
┌────────┐  ┌────────────────────┐
│CERRADA │  │ REVISAR RESOLUCIÓN │ ◀── Control pide correcciones
└────────┘  └─────────┬──────────┘
                      │
                      └──▶ (Vuelve a RESUELTA o VALORADA según corrección)

        ↓ (En cualquier momento)
    ┌────────────┐
    │  ANULADA   │ ◀── Asignación anulada por Control
    └────────────┘
```

#### Descripción de Estados Proveedor

| Estado | Significado | Quién lo pone |
|--------|-------------|---------------|
| **Abierta** | Incidencia asignada, sin empezar | Automático al asignar |
| **En resolución** | Trabajando sin necesidad de presupuesto | Proveedor (al calendarizar visita) |
| **Ofertada** | Presupuesto enviado, pendiente de aprobación | Proveedor (al enviar presupuesto) |
| **Oferta aprobada** | Control aprobó el presupuesto | Control |
| **Oferta a revisar** | Control pide cambios en el presupuesto | Control |
| **Resuelta** | Trabajo técnico completado | Proveedor |
| **Valorada** | Valoración económica completada | Proveedor |
| **Revisar resolución** | Control pide correcciones | Control |
| **Cerrada** | Todo aprobado y cerrado | Control |
| **Anulada** | Asignación cancelada | Control |

---

## 4. Flujo de Asignación de Proveedor

### 4.1 Proceso de Asignación

Solo **Control** puede asignar proveedores a las incidencias.

**Pasos:**
1. Control abre una incidencia sin proveedor
2. Selecciona un proveedor de la lista
3. Define la prioridad (Crítico / No crítico)
4. Opcionalmente añade descripción para el proveedor
5. Puede excluir ciertas imágenes de la vista del proveedor
6. Puede adjuntar documentos adicionales

**Al asignar:**
- El estado cliente cambia a **"En tramitación"**
- El estado proveedor se crea como **"Abierta"**
- El proveedor recibe notificación
- Se crea un chat privado entre Control y Proveedor

### 4.2 Prioridades del Proveedor

| Prioridad | Significado |
|-----------|-------------|
| **Crítico** | Requiere atención urgente |
| **No crítico** | Puede planificarse con normalidad |

### 4.3 Proveedores Externos (Nuevos)

Control puede asignar un proveedor que no existe en el sistema:

- Introduce el CIF del proveedor externo
- Opcionalmente introduce el nombre
- El sistema crea automáticamente el proveedor
- La incidencia se asigna al nuevo proveedor

### 4.4 Reasignación de Proveedor

**Cuándo ocurre:**
- Control decide cambiar de proveedor
- El proveedor anterior se anula y se asigna uno nuevo

**Proceso:**
1. Control anula la asignación actual (debe indicar motivo)
2. Las citas programadas se cancelan automáticamente
3. Control asigna un nuevo proveedor
4. Se crea un nuevo chat con el nuevo proveedor

**Nota:** El historial del proveedor anterior se mantiene para trazabilidad.

---

## 5. Flujo de Presupuestos

### 5.1 Cuándo se Necesita Presupuesto

El presupuesto es **opcional**. El proveedor decide si enviar presupuesto o resolver directamente.

**Casos típicos con presupuesto:**
- Trabajos de importe significativo
- Obras o reparaciones mayores
- Cuando el cliente lo solicita

**Casos típicos sin presupuesto:**
- Reparaciones menores
- Mantenimiento preventivo rutinario
- Urgencias inmediatas

### 5.2 Creación del Presupuesto

**¿Quién lo crea?** El Proveedor

**Datos obligatorios:**
- Fecha estimada de inicio
- Duración estimada del trabajo
- Importe total sin IVA
- Documento del presupuesto detallado (PDF, Excel, Word)
- Descripción del trabajo a realizar

### 5.3 Estados del Presupuesto

```
┌───────────────────┐
│PENDIENTE_REVISION │ ◀── Recién enviado por proveedor
└────────┬──────────┘
         │
    ┌────┴────┐
    │         │
    ▼         ▼
┌────────┐  ┌──────────┐
│APROBADO│  │RECHAZADO │ ◀── Requiere modificaciones
└────────┘  └─────┬────┘
                  │
                  └──▶ (Proveedor envía nuevo presupuesto)
```

### 5.4 Aprobación de Presupuestos

**¿Quién aprueba?** Solo Control

**Proceso de aprobación:**
1. Control revisa el presupuesto recibido
2. Puede descargar y revisar el documento adjunto
3. **Aprobar:** El proveedor puede proceder con el trabajo
4. **Mandar a revisar:** Control indica qué debe cambiar

**Al aprobar:**
- Estado proveedor cambia a **"Oferta aprobada"**
- El proveedor recibe notificación
- El proveedor puede proceder con la ejecución

**Al rechazar (mandar a revisar):**
- Estado proveedor cambia a **"Oferta a revisar"**
- Control indica el motivo del rechazo
- El proveedor recibe notificación
- El proveedor debe enviar un nuevo presupuesto corregido

---

## 6. Flujo de Resolución

### 6.1 Opciones del Proveedor

Una vez asignado, el proveedor puede:

1. **Calendarizar visita** - Programar cuándo irá al centro
2. **Enviar presupuesto** - Si el trabajo requiere aprobación previa
3. **Resolver directamente** - Si no necesita presupuesto

### 6.2 Calendarización de Visita

**Datos requeridos:**
- Fecha de la visita
- Horario (Mañana / Tarde / Fuera de horario)

**Efecto:**
- La visita queda registrada en el calendario
- El estado proveedor puede cambiar a **"En resolución"**
- Control y Cliente pueden ver la visita programada

### 6.3 Resolución Técnica

**¿Quién la hace?** El Proveedor

**Datos obligatorios:**
- Descripción de la solución aplicada
- Fecha de realización del trabajo

**Datos opcionales:**
- Imágenes de evidencia (fotos del trabajo terminado)
- Parte de trabajo (documento)

**Efecto:**
- Estado proveedor cambia a **"Resuelta"**
- Estado cliente cambia a **"Resuelta"**
- Se espera la valoración económica

### 6.4 Valoración Económica

**¿Quién la hace?** El Proveedor (después de la resolución técnica)

**Datos obligatorios:**
- Importe sin IVA
- Porcentaje de IVA aplicado
- Importe con IVA (calculado automáticamente)
- Documento justificativo (si el importe difiere del presupuesto aprobado)

**Excepción:** Si el importe coincide exactamente con el presupuesto aprobado, no se requiere documento adicional.

**Efecto:**
- Estado proveedor cambia a **"Valorada"**
- La incidencia queda lista para que Control la cierre

---

## 7. Flujo de Cierre

### 7.1 Cierre Normal

**¿Quién cierra?** Solo Control

**Requisitos para cerrar:**
- El proveedor debe haber completado la resolución técnica
- El proveedor debe haber completado la valoración económica

**Proceso:**
1. Control revisa la resolución técnica
2. Control revisa la valoración económica
3. Control aprueba y cierra (puede añadir comentario)

**Efecto:**
- Estado proveedor cambia a **"Cerrada"**
- Estado cliente cambia a **"Cerrada"**
- Se registra el mes de cierre

### 7.2 Rechazo de Resolución

Si Control no está conforme con la resolución o valoración:

**Tipos de rechazo:**
- **Técnica:** La resolución técnica necesita corrección
- **Económica:** La valoración económica necesita corrección
- **Ambas:** Tanto técnica como económica necesitan corrección

**Proceso:**
1. Control selecciona qué necesita corrección
2. Control indica el motivo detallado
3. Estado proveedor cambia a **"Revisar resolución"**
4. El proveedor recibe notificación
5. El proveedor corrige lo indicado
6. El proveedor vuelve a enviar
7. Control revisa de nuevo

### 7.3 Resolución Manual (Excepcional)

Control puede resolver una incidencia manualmente sin intervención completa del proveedor.

**Datos requeridos:**
- Descripción de la resolución
- Proveedor externo (opcional)
- Importe (opcional)
- Documentos justificativos (opcional)

**Cuándo se usa:**
- Urgencias resueltas por medios alternativos
- Trabajos realizados por personal interno
- Situaciones excepcionales

---

## 8. Comunicación

### 8.1 Sistema de Chat Dual

Cada incidencia tiene **dos canales de chat** separados:

| Chat | Participantes | Contenido |
|------|---------------|-----------|
| **Chat Cliente** | Control ↔ Cliente/Gestor | Comunicación sobre la incidencia |
| **Chat Proveedor** | Control ↔ Proveedor | Coordinación técnica y presupuestos |

**Importante:** El Cliente/Gestor NO ve el chat con el Proveedor y viceversa.

### 8.2 Mensajes del Sistema

El sistema genera mensajes automáticos cuando:
- Se asigna un proveedor
- Se envía un presupuesto
- Se aprueba/rechaza un presupuesto
- Se completa la resolución
- Se cierra la incidencia
- Se anula una asignación

### 8.3 Adjuntos en Chat

Los usuarios pueden adjuntar:
- Imágenes
- Documentos (PDF, Word, Excel)
- Presupuestos
- Partes de trabajo

**Visibilidad de adjuntos:**
- Control puede marcar imágenes como "no visibles" para el proveedor
- Los documentos del proveedor son visibles para Control
- El Cliente solo ve los adjuntos del chat cliente

---

## 9. Notificaciones

### 9.1 Notificaciones al Proveedor

El proveedor recibe notificación cuando:
- Se le asigna una nueva incidencia
- Se aprueba su presupuesto
- Su presupuesto requiere revisión
- Su resolución requiere corrección
- Se anula su asignación
- Recibe un nuevo mensaje en el chat

### 9.2 Indicadores Visuales

El sistema muestra badges/indicadores para:
- Incidencias con presupuesto pendiente de revisión
- Incidencias con resolución pendiente de aprobación
- Mensajes no leídos en el chat
- Visitas programadas próximas

---

## 10. Casos Especiales

### 10.1 Poner en Espera

Control puede pausar una incidencia temporalmente.

**Motivos típicos:**
- Esperando decisión del cliente
- Esperando materiales
- Esperando aprobación de presupuesto externo
- Fuerza mayor

**Efecto:**
- Estado cliente cambia a **"En espera"**
- Se registra el motivo

### 10.2 Anulación de Incidencia

Control puede anular una incidencia completa (no solo la asignación del proveedor).

**Motivos típicos:**
- Incidencia duplicada
- Ya no procede
- Error en la creación

**Efecto:**
- Estado cliente cambia a **"Anulada"**
- La incidencia queda inactiva

### 10.3 Anulación de Asignación de Proveedor

Control puede anular solo la asignación del proveedor, manteniendo la incidencia activa.

**Motivos típicos:**
- Proveedor no disponible
- Cambio de proveedor por mejor oferta
- Proveedor no responde

**Efecto:**
- Estado proveedor cambia a **"Anulada"**
- Se cancelan las citas programadas
- Se notifica al proveedor
- La incidencia puede reasignarse a otro proveedor

### 10.4 Edición de Resolución

El proveedor puede editar una resolución ya enviada mientras no esté cerrada.

**Se puede editar:**
- Descripción de la solución
- Fecha de realización
- Imágenes de evidencia
- Valoración económica

**No se puede editar después de:**
- Control cierra la incidencia

---

## 11. Dashboard y Métricas

### 11.1 Dashboard Cliente/Gestor

Muestra el conteo de incidencias por estado:
- Abiertas
- En espera
- En tramitación
- Resueltas
- Cerradas
- Anuladas

**Gestores:** Además ven un desglose por cada centro asignado.

### 11.2 Dashboard Control

Muestra dos vistas alternables:
- **Vista Cliente:** Estados desde la perspectiva del cliente
- **Vista Proveedor:** Estados desde la perspectiva del proveedor

### 11.3 Dashboard Proveedor

Muestra sus incidencias asignadas:
- Nuevas (pendientes de atender)
- En proceso
- Pendientes de aprobación
- Con revisiones pendientes

---

## 12. Historial y Auditoría

### 12.1 Registro de Cambios de Estado

El sistema registra automáticamente:
- Cada cambio de estado (cliente y proveedor)
- Quién realizó el cambio
- Cuándo se realizó
- Motivo del cambio (si aplica)
- Metadatos adicionales (importes, acciones específicas)

### 12.2 Historial de Proveedores

Para cada incidencia se mantiene:
- Todos los proveedores que han sido asignados
- Motivo de cada anulación
- Fechas de asignación y anulación

### 12.3 Trazabilidad Completa

Se puede consultar:
- Historial completo de estados
- Todos los mensajes intercambiados
- Todos los documentos adjuntados
- Todas las visitas programadas
- Presupuestos enviados y sus estados

---

## Anexo A: Glosario de Términos

| Término | Definición |
|---------|------------|
| **Incidencia** | Avería, problema o solicitud de mantenimiento en un centro |
| **Centro** | Institución/residencia donde ocurre la incidencia |
| **Proveedor** | Empresa externa de mantenimiento |
| **Presupuesto** | Oferta económica del proveedor para realizar un trabajo |
| **Resolución técnica** | Documentación del trabajo realizado |
| **Valoración económica** | Coste final del trabajo ejecutado |
| **Control** | Administrador central que coordina todo el sistema |
| **Gestor** | Responsable de uno o varios centros |
| **Proveedor caso** | Asignación específica de un proveedor a una incidencia |

---

## Anexo B: Diagrama de Estados Completo

```
                                    ┌──────────────────────────────────────────────────────────┐
                                    │                    VISTA CLIENTE                          │
                                    └──────────────────────────────────────────────────────────┘

                                          ┌──────────┐
                                          │ ABIERTA  │
                                          └────┬─────┘
                                               │
                            ┌──────────────────┼──────────────────┐
                            ▼                  ▼                  ▼
                     ┌───────────┐      ┌─────────────┐    ┌───────────┐
                     │ EN ESPERA │      │EN TRAMITACIÓN│    │  ANULADA  │
                     └─────┬─────┘      └──────┬──────┘    └───────────┘
                           │                   │
                           └─────────┬─────────┘
                                     ▼
                              ┌───────────┐
                              │  RESUELTA │
                              └─────┬─────┘
                                    │
                                    ▼
                              ┌───────────┐
                              │  CERRADA  │
                              └───────────┘


                                    ┌──────────────────────────────────────────────────────────┐
                                    │                   VISTA PROVEEDOR                         │
                                    └──────────────────────────────────────────────────────────┘

                                          ┌──────────┐
                                          │ ABIERTA  │◀────────────────────────────────┐
                                          └────┬─────┘                                 │
                                               │                                       │
                       ┌───────────────────────┼───────────────────────┐               │
                       ▼                       ▼                       ▼               │
               ┌──────────────┐         ┌───────────┐          ┌───────────┐          │
               │EN RESOLUCIÓN │         │  OFERTADA │          │  ANULADA  │          │
               └──────┬───────┘         └─────┬─────┘          └───────────┘          │
                      │                       │                                        │
                      │              ┌────────┴────────┐                               │
                      │              ▼                 ▼                               │
                      │      ┌──────────────┐  ┌─────────────────┐                    │
                      │      │OFERTA APROBADA│  │OFERTA A REVISAR │────────────────────┤
                      │      └──────┬───────┘  └─────────────────┘                    │
                      │             │                                                  │
                      └──────────┬──┘                                                  │
                                 ▼                                                     │
                          ┌───────────┐                                                │
                          │  RESUELTA │                                                │
                          └─────┬─────┘                                                │
                                │                                                      │
                                ▼                                                      │
                          ┌───────────┐                                                │
                          │  VALORADA │                                                │
                          └─────┬─────┘                                                │
                                │                                                      │
                       ┌────────┴────────┐                                             │
                       ▼                 ▼                                             │
                ┌───────────┐   ┌──────────────────┐                                   │
                │  CERRADA  │   │REVISAR RESOLUCIÓN│───────────────────────────────────┘
                └───────────┘   └──────────────────┘
```

---

*Documento creado: Enero 2026*
*Última actualización: Enero 2026*
*Este documento es la fuente única de verdad para el desarrollo del sistema de gestión de incidencias de Fundación La Caixa.*
