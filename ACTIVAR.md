# Web de emergencia de #Okservice.es — cómo activarla

Para cuando el VPS (185.172.57.99) está caído y con él **okservice.es, electricistatenerife.es
y los bots**. Eres un servicio de averías 24h: lo que no puede pasar es que alguien con la luz
cortada entre en tu web y no encuentre el teléfono.

`index.html` son **6,4 KB sin una sola dependencia externa**: ni fuentes de Google, ni CSS aparte,
ni JavaScript, ni imágenes remotas. Si la infraestructura se está cayendo, cada cosa que haya que
ir a buscar a otro sitio es otra cosa que puede fallar.

---

## A tu favor: el TTL del DNS es de 60 segundos

```
okservice.es.  60  IN  A  185.172.57.99
```

Un cambio de IP en IONOS se propaga en **un minuto**. Eso hace viable el plan manual.

---

## Plan A — manual, disponible hoy (unos 10 minutos)

Hace falta que la página esté ya alojada fuera del VPS. Ver «Dónde alojarla», abajo.

1. Entra en **IONOS → Dominios → okservice.es → DNS**.
2. Cambia el registro **A** de `185.172.57.99` a la IP del alojamiento de emergencia.
3. Espera 1-2 minutos y comprueba: `dig +short okservice.es`.

**Para volver:** el mismo registro A de vuelta a `185.172.57.99`. Otro minuto.

⚠️ **El HTTPS es el punto débil de este plan.** El certificado de okservice.es lo emite el VPS
(Traefik/Let's Encrypt). Si apuntas el dominio a otro sitio, ese otro sitio necesita su propio
certificado para `okservice.es`, y emitirlo tarda. Mientras tanto el navegador avisa de sitio no
seguro, que para un cliente nuevo es peor que una web caída. Por eso existe el plan B.

---

## Plan B — Cloudflare, el que de verdad resuelve esto (recomendado)

Ya está escrito y sin aplicar en `okservice-landing`: `despliegue/CLOUDFLARE.md`, decisión de
Jose del 9-10-2026.

Con Cloudflare delante del dominio:

- **«Always Online»** sirve una copia cacheada de la web si el origen no responde. Sin tocar nada.
- El **certificado lo da Cloudflare**, así que el HTTPS nunca se rompe aunque el VPS no esté.
- Puedes definir una **página de error personalizada** que sea exactamente esta.
- De paso descarga al VPS de servir el marketing, que es el motivo por el que se planteó.

Es el mismo trabajo que ya estaba planificado, y resuelve la emergencia **de forma automática**
en vez de manual. Si solo se va a hacer una cosa de esta lista, que sea esta.

---

## Dónde alojarla (fuera del VPS, obviamente)

| Opción | Coste | Repo privado | Nota |
|---|---|---|---|
| **Cloudflare Pages** | gratis | ✅ sí | La mejor si ya vas a usar Cloudflare |
| **Netlify / Vercel** | gratis | ✅ sí | Despliegue en un minuto |
| **GitHub Pages** | gratis | ❌ exige repo PÚBLICO en plan Free | Ojo: un repo público de tu web es justo lo que hizo a una IA describirte como «empresa relativamente nueva» |

**Lo que NO vale:** alojarla en el mismo VPS. Caería con él, que es el escenario del que hablamos.

---

## Mantenimiento

Revísala **cada vez que cambie el teléfono, la dirección o el nº de instalador**. Nada más: no
lleva precios a propósito, porque una página que se toca una vez al año acaba mintiendo, y un
precio equivocado durante una caída es peor que no dar precio.

Datos que lleva ahora (verificados el 14-10-2026 contra Odoo y el NAP canónico):

- VILLVERG SOLUCIONES INTEGRALES SL · CIF B19430115
- Instalador autorizado de baja tensión nº 25094
- C. Ángel Arocha 24, 38594 Granadilla de Abona
- Teléfono y WhatsApp: 679 32 52 22 · info@okservice.es
- e-distribución (averías de la red): 900 878 212

---

## Probarla en local

```bash
cd ~/okservice-emergencia && python3 -m http.server 8877
# y abre http://127.0.0.1:8877/
```
