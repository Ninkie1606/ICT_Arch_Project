# Metadata Management en Object Storage PoC

Dit project dient als Proof of Concept (PoC) voor een schaalbaar archiefsysteem, ontwikkeld voor een onderzoeksafdeling geschiedenis. Het doel is om aan te tonen hoe metadata en fysieke bestanden (blobs) gescheiden kunnen worden opgeslagen om de prestaties en integriteit van het archief te waarborgen.

## Architectuur & Design Beslissingen

De oplossing is gebaseerd op een gescheiden opslagstrategie waarbij data-integriteit en security (RBAC) centraal staan.

- **PostgreSQL**: Fungeert als de Metadata Store. Hier worden documenteigenschappen, versienummers, rollen (`role_id`) en integriteit-hashes (`checksum`) bijgehouden.
- **MinIO**: Fungeert als de Object Store. Hier worden de daadwerkelijke scans en documenten (blobs) opgeslagen in buckets.
- **pgAdmin**: Wordt gebruikt voor het beheer van de relationele data en het uitvoeren van queries.

### Documentatie van Beslissingen

Voor een gedetailleerde onderbouwing van de gemaakte keuzes (zoals de keuze voor SHA-256 en de scheiding van opslag), zie:

- **[ADR.md](./ADR.md)**: Architecture Decision Record.

## Visualisatie (C4-Model)

Om de structuur van dit project inzichtelijk te maken, zijn er C4-diagrammen (Context & Container) opgesteld:

- **[C4-POC/](./C4-POC/)**: Bevat de diagrammen en Structurizr DSL van de huidige Docker-opzet.
- **[C4-POC-toekomstig/](./C4-POC-toekomstig/)**: Bevat de blauwdruk voor een volledige applicatie, inclusief een Frontend SPA en Backend API, mocht dit project verder ontwikkeld worden.

## Gegevensstructuur

Het systeem gebruikt drie kern-tabellen om de data-integriteit en veiligheid te bewaken:

1.  **documents**: Bevat primaire informatie, de originele bestandsnaam en de `role_id` voor toegangscontrole.
2.  **document_versions**: Beheert de koppeling naar MinIO via een unieke key en bevat de SHA-256 checksum voor integriteitscontrole.
3.  **audit_trail**: Logt elke actie (zoals uploads) voor volledige traceerbaarheid en onweerlegbaarheid.

## Installatie en Gebruik

### 1. Infrastructuur opstarten

De omgeving wordt beheerd via Docker Compose. Zorg dat je een .env bestand hebt geconfigureerd (zie example.env) en start de containers met:

docker-compose up -d

### 2. Inrichting

Volg de stappen in de **[HANDLEIDING.md](./HANDLEIDING.md)** HANDLEIDING.md voor het uploaden van de testbestanden naar MinIO en het initialiseren van de database-metadata via pgAdmin.
