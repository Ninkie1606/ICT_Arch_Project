# Metadata Management en Object Storage PoC

Dit project dient als Proof of Concept (PoC) voor een schaalbaar archiefsysteem, ontwikkeld voor een onderzoeksafdeling geschiedenis. Het doel is om aan te tonen hoe metadata en fysieke bestanden (blobs) gescheiden kunnen worden opgeslagen om de prestaties en integriteit van het archief te waarborgen.

## Architectuur

De oplossing is gebaseerd op een gescheiden opslagstrategie:

- **PostgreSQL**: Fungeert als de Metadata Store. Hier worden documenteigenschappen, versienummers en audit-gegevens bijgehouden.
- **MinIO**: Fungeert als de Object Store. Hier worden de daadwerkelijke scans en documenten (blobs) opgeslagen in buckets.
- **pgAdmin**: Wordt gebruikt voor het beheer van de relationele data en het uitvoeren van queries.

Door deze componenten te scheiden, blijft de database klein en snel, terwijl de opslag van zware bestanden onbeperkt kan schalen op een gespecialiseerd platform.

## Gegevensstructuur

Het systeem gebruikt drie kern-tabellen om de data-integriteit te bewaken:

1.  **documents**: Bevat de primaire informatie zoals de titel en de originele bestandsnaam.
2.  **document_versions**: Beheert de koppeling naar MinIO via een unieke key en houdt versienummers bij.
3.  **audit_trail**: Logt elke actie (zoals uploads) voor volledige traceerbaarheid van wijzigingen.

## Installatie en Gebruik

### 1. Infrastructuur opstarten

De omgeving wordt beheerd via Docker Compose. Start de containers met het volgende commando:

```bash
docker-compose up -d
```
