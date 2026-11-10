# MuniDigital

Portal de Trámites y Pagos Municipales Seguros, desarrollado bajo un ciclo DevSecOps.

- **Asignatura:** Desarrollo Seguro (CI3V64), INACAP Santiago Sur
- **Docente:** Mario Yáñez Urrutia
- **Equipo:** Diego Sandoval (Scrum Master), Darío Ricci (Security Champion), Julio Adones (Desarrollador), Jorge Tobar (DevOps / QA)

## Descripción

Plataforma para que vecinos y contribuyentes consulten y paguen trámites municipales:

1. Patentes comerciales (consulta y validación de vigencia).
2. Permisos municipales y derechos de aseo.
3. Consultas del Juzgado de Policía Local (aisladas por RUT / patente).
4. Pagos con Transbank Webpay Plus (SDK oficial, ambiente de integración).

No almacena números de tarjeta ni CVV (PCI-DSS delegado a Transbank). Los datos personales se tratan según la Ley 21.719.

## Arquitectura

| Zona | Componentes |
|---|---|
| Zona 1: Internet | Frontend React 18 en el navegador |
| Zona 2: DMZ | Ingress Gateway (TLS 1.3) y API Django 5 en contenedores de mínimo privilegio |
| Zona 3: red privada | PostgreSQL 16 sin IP pública y Azure Key Vault |

Transbank Webpay es un servicio externo. Los límites de confianza A (Internet ↔ DMZ) y B (DMZ ↔ datos / pasarela) están documentados en el informe.

## Stack

- **Backend:** Django 5 (Python), Django ORM parametrizado
- **Frontend:** React 18
- **Base de datos:** PostgreSQL 16
- **CI/CD y seguridad:** GitHub Actions, Bandit (SAST), pip-audit y npm audit (SCA), detect-secrets (secretos)
- **Gestión:** Azure Boards, Azure Key Vault

## Estructura del repositorio

```
MuniDigital/
├── backend/     # API Django (por agregar)
├── frontend/    # Aplicación React (por agregar)
├── docs/        # Informes de evaluación en PDF
├── .github/     # Workflows de CI y plantilla de PR
├── .gitignore
└── README.md
```

## Ejecución local

> Completar cuando el código esté disponible.

```bash
# Backend
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env        # completar con valores de prueba
python manage.py migrate
python manage.py runserver

# Frontend
cd frontend
npm install
npm start
```

Nunca subas el archivo `.env` ni credenciales reales.

## Pruebas y chequeos de seguridad

```bash
pytest                                  # pruebas unitarias e integración
bandit -r backend                       # análisis estático (SAST)
pip-audit -r backend/requirements.txt   # vulnerabilidades en dependencias (SCA)
npm audit --prefix frontend             # dependencias del frontend
pre-commit run --all-files              # detect-secrets
```

## Flujo de trabajo

- La rama `main` está protegida: no se permite push directo.
- Todo cambio entra por Pull Request con al menos 1 aprobación y los chequeos automáticos en verde.
- Cada commit y PR incluye el Work Item de Azure Boards con el formato `AB#<ID>`.
  Ejemplo: `feat: add Webpay controller AB#104`

## Documentación

Los informes de evaluación en PDF están en [`docs/`](docs/).

