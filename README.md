# SEGI — Sistema de Evacuação Geoespacial Inteligente

> Plataforma de resposta a emergências com rotas de fuga, alertas em tempo real e operação offline.

![Status](https://img.shields.io/badge/status-em%20desenvolvimento-orange)
![Licença](https://img.shields.io/badge/licença-Apache%202.0-blue)
![Stack](https://img.shields.io/badge/stack-Flutter%20·%20FastAPI%20·%20PostGIS-informational)

---

## Sobre o Projeto

O SEGI é um sistema crítico de evacuação para áreas de risco — rompimento de barragens, inundações e eventos similares. Combina dados geoespaciais, inteligência artificial e operação offline para guiar populações até zonas seguras mesmo nos piores cenários de desastre.

**3 pilares fundamentais:**

- **Antecipar Risco** — IA analisa dados topográficos e pluviométricos antes que a situação se torne crítica
- **Guiar em Tempo Real** — rotas de fuga calculadas dinamicamente, evitando zonas de inundação
- **Operar Offline** — mapas e rotas pré-carregadas no dispositivo, sem necessidade de internet

---

## Demo

🔗 [segi-app.vercel.app](https://segi-app.vercel.app)

---

## Funcionalidades

- 📍 Localização em tempo real via GPS (funciona offline)
- 🌊 Visualização de zonas de risco com classificação por cores
- 🚨 Alertas de emergência via push notification e SMS fallback
- 🧭 Rotas de fuga inteligentes com recálculo dinâmico
- 🏥 Mapa de abrigos com capacidade e distância
- 📡 Funcionamento 100% offline com sincronização automática
- ⚙️ Modo técnico com camadas GIS para operadores

---

## Arquitetura

```
┌─────────────────────────────────────────────────┐
│              App Mobile (Flutter)               │
│     Mapa offline · GPS · Alertas · Rotas        │
└────────────────────┬────────────────────────────┘
                     │ REST / WebSocket
┌────────────────────▼────────────────────────────┐
│           Backend API (Python + FastAPI)        │
│     Roteamento · GeoIA · Integração CEMADEN     │
└───────────┬─────────────────────┬───────────────┘
            │                     │
┌───────────▼──────────┐ ┌────────▼──────────────┐
│ PostgreSQL + PostGIS │ │  QGIS · GeoServer     │
│ pgRouting · Abrigos  │ │  GDAL · Shapefiles    │
└──────────────────────┘ └───────────────────────┘
```

---

## Stack Tecnológico

| Camada | Tecnologia |
|---|---|
| App Mobile | Flutter (Dart) |
| Backend | Python 3.11 + FastAPI |
| Banco de Dados | PostgreSQL 15 + PostGIS 3 |
| Roteamento | pgRouting |
| GIS | QGIS · GeoServer · GDAL |
| Inteligência Artificial | GeoIA (Random Forest) |
| Notificações | FCM (push) · SMS fallback |
| Deploy | Vercel (frontend) · Railway/Render (API) |

---

## Banco de Dados Geoespacial

```sql
-- Zonas de risco (polígonos)
CREATE TABLE areas_risco (
  id SERIAL PRIMARY KEY,
  geom GEOMETRY(POLYGON, 4674),
  nivel_risco INTEGER,  -- 1=baixo 2=médio 3=alto 4=crítico
  tipo VARCHAR(50),
  atualizado_em TIMESTAMP DEFAULT NOW()
);

-- Rotas de fuga (linhas)
CREATE TABLE rotas_fuga (
  id SERIAL PRIMARY KEY,
  geom GEOMETRY(LINESTRING, 4674),
  tempo_estimado INTEGER,  -- segundos
  distancia_m FLOAT,
  altitude_ganho FLOAT
);

-- Abrigos (pontos)
CREATE TABLE abrigos (
  id SERIAL PRIMARY KEY,
  nome VARCHAR(100),
  geom GEOMETRY(POINT, 4674),
  capacidade INTEGER,
  ocupacao_atual INTEGER
);
```

---

## Como Executar

### Pré-requisitos

- Flutter SDK 3.x
- Python 3.11+
- PostgreSQL 15 + PostGIS 3
- Docker (recomendado)

### Backend

```bash
git clone https://github.com/wrg-tech/segi-app.git
cd segi-app/backend

python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

pip install -r requirements.txt

cp .env.example .env  # configure DATABASE_URL

uvicorn main:app --reload
```

### Banco de Dados

```bash
docker run --name segi-db \
  -e POSTGRES_PASSWORD=segi123 \
  -p 5432:5432 \
  -d postgis/postgis:15-3.3

psql -U postgres -d segi -f schema.sql
```

### App Flutter

```bash
cd segi-app/mobile
flutter pub get
flutter run
```

---

## Estrutura do Repositório

```
segi-app/
├── mobile/          # App Flutter
│   ├── lib/
│   │   ├── screens/ # Telas do app
│   │   ├── services/# GPS, API, offline
│   │   └── widgets/ # Componentes
├── backend/         # API FastAPI
│   ├── routes/      # Endpoints
│   ├── models/      # Schemas Pydantic
│   └── geoai/       # Módulo de IA
├── database/        # SQL schemas e migrations
├── gis/             # Arquivos QGIS e shapefiles
├── docs/            # Documentação técnica
└── prototype/       # Protótipo HTML (Vercel)
    └── index.html
```

---

## Roadmap

- [x] Estrutura do banco geoespacial
- [ ] API FastAPI básica
- [ ] App Flutter com mapa offline
- [ ] Integração GeoServer
- [ ] Módulo GeoIA
- [ ] Piloto em município de risco
- [ ] Integração CEMADEN / ANA

---

## Dados Geoespaciais

O sistema utiliza projeção **EPSG:4674 (SIRGAS 2000)**, padrão brasileiro. Fontes de dados:

- **MDT**: Modelo Digital do Terreno (resolução 10m × 10m)
- **Rede viária**: OpenStreetMap + shapefile municipal
- **Dados pluviométricos**: CEMADEN / ANA
- **Barragens**: SNISB (Sistema Nacional de Informações sobre Segurança de Barragens)

---

## Aviso Legal

Este sistema é destinado a uso em emergências críticas. A precisão dos dados geoespaciais e a validação das rotas são de responsabilidade dos operadores e autoridades competentes. O uso em produção requer parceria formal com órgãos de Defesa Civil.

---

## Licença

Copyright 2025 WRG TECH

Licenciado sob a Apache License, Version 2.0. Consulte o arquivo [LICENSE](./LICENSE) para detalhes.

---

Desenvolvido por **WRG TECH** · Tecnologia Crítica · GovTech · Disaster Response
