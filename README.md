# infra

Infraestructura Docker para publicacion, gestion y acceso remoto:

- Cloudflared (tunel Cloudflare)
- Arcane (gestion de proyectos/servicios Docker)
- Tailscale (acceso remoto)
- Heimdall (dashboard de aplicaciones)

## Estructura

```text
infra/
├── .env.example
├── .gitignore
├── README.md
├── start.sh
├── stop.sh
├── cloudflared/
│   ├── compose.yaml
│   └── .env -> ../.env
├── arcane/
│   ├── compose.yaml
│   └── .env -> ../.env
├── tailscale/
│   ├── compose.yaml
│   └── .env -> ../.env
└── heimdall/
   ├── compose.yaml
   └── .env -> ../.env
```

## Preparacion

1. Copiar variables:
   - cp .env.example .env
2. Completar secretos en .env:
   - CLOUDFLARE_TOKEN
   - ENCRYPTION_KEY
   - JWT_SECRET
   - TS_AUTHKEY
3. Revisar PROJECTS_DIRECTORY para que apunte a tu raiz de proyectos.

### Generar secretos para Arcane

- Opcion hex (64 caracteres):
   - openssl rand -hex 32
- Opcion base64 (aprox 43-44 caracteres):
   - openssl rand -base64 32

Ejemplo para generarlos y guardarlos en `.env` (persistente):

```bash
enc="$(openssl rand -hex 32)"
jwt="$(openssl rand -hex 32)"
perl -i.bak -pe "s/^ENCRYPTION_KEY=.*/ENCRYPTION_KEY=$enc/; s/^JWT_SECRET=.*/JWT_SECRET=$jwt/" .env
```

Esto escribe valores reales en el archivo `.env`. Si reinicias, siguen ahi hasta que los cambies.

## Arranque

- ./start.sh

Tambien puedes arrancar cada stack desde su carpeta sin parametros extra:

- cd cloudflared && docker compose up -d
- cd arcane && docker compose up -d
- cd tailscale && docker compose up -d
- cd heimdall && docker compose up -d

## Parada

- ./stop.sh

Desde cada carpeta:

- docker compose down

## Notas

- Arcane monta PROJECTS_DIRECTORY del host y usa esa misma ruta dentro del contenedor.
- Se crea automaticamente la red Docker externa proxy si no existe.
- El stack de Tailscale en este compose esta orientado a host Linux (network_mode host y /dev/net/tun).
