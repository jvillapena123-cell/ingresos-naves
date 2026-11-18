# Ingresos por Nave · HTP

App web con clave para registrar naves y sus ingresos/servicios según el Tarifado Público de HTP. Usa Firebase (login + base de datos) y se publica en GitHub Pages.

## 1. Firebase (una sola vez)

1. Entre a https://console.firebase.google.com → **Agregar proyecto** (ej. `ingresos-naves-htp`).
2. **Authentication → Comenzar → Correo electrónico/contraseña → Habilitar.**
3. En **Authentication → Users → Agregar usuario**, cree cada persona con acceso (correo + clave). No hay registro público: sólo entra quien usted cree aquí.
4. **Firestore Database → Crear base de datos** (modo producción, región `southamerica-west1` Santiago).
5. En **Firestore → Reglas**, pegue y publique:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

6. **Configuración del proyecto (engranaje) → Tus apps → Web (</>)** → registre la app y copie el bloque `firebaseConfig`.
7. Abra `index.html`, busque `FIREBASE_CONFIG` y reemplace los valores `PEGAR_AQUI` con los suyos.
8. En **Authentication → Settings → Dominios autorizados**, agregue `jvillapena123-cell.github.io`.

## 2. GitHub Pages

1. Cree un repositorio nuevo, por ejemplo `ingresos-naves`.
2. Suba `index.html` (y este README).
3. **Settings → Pages → Branch: main / (root) → Save.**
4. Queda en `https://jvillapena123-cell.github.io/ingresos-naves/`

La config de Firebase en el HTML no es secreta (es normal que sea pública); la seguridad la dan el login y las reglas de Firestore.

## 3. Uso

- **Naves:** listado con totales, filtros y exportación a Excel (resumen + detalle de servicios).
- **Nueva nave:** datos de la nave (eslora, toneladas, atraque/desatraque) y se marcan los ítems del tarifado que aplican. La cantidad se calcula sola:
  - Metro-Eslora-Hora → eslora × horas de estadía
  - Hora Nave → horas de estadía
  - Tonelada → toneladas de la nave
  - El resto (turnos, eventos, faenas, m³…) se ingresa a mano.
- **Tarifas:** descargue la plantilla Excel, cambie valores y vuelva a subirla indicando la vigencia. Se muestra una vista previa con los cambios en naranja antes de publicar. Cada carga queda en el historial.
- **Tarifa por nave:** en cada nave la tarifa de cada ítem es editable. Si difiere de la pública queda en naranja y muestra la pública debajo; la nave se marca "Tarifa especial" en el listado. También puede cargar un Excel de tarifas sólo para esa nave (mismo formato de plantilla), descargar las tarifas de la nave o restablecer las públicas. El Excel exportado muestra tarifa pública vs. aplicada.
- **Dólar observado:** al ingresar la fecha de desatraque la app trae el dólar observado de ese día (Banco Central vía mindicador.cl). Si cae fin de semana o feriado usa el último día hábil anterior. Se puede escribir a mano. Calcula el total en pesos y se guarda con la nave.
- Cada nave guarda la tarifa con que se registró: actualizar el tarifado no altera naves ya cargadas.

## Modo demo

Mientras `FIREBASE_CONFIG` tenga `PEGAR_AQUI`, la app funciona sin nube (datos sólo en el navegador) con clave `htp2026`, para probarla.

## Datos en Firestore

- `tarifas/vigente` — tarifado actual
- `tarifas_historial/*` — cada carga de tarifas
- `naves/*` — cada nave con sus servicios y total
