# Technische Handleiding: Inrichting PoC Archiefsysteem

Deze handleiding beschrijft de stappen die zijn ondernomen om de Proof of Concept (PoC) omgeving in te richten, de data te laden en de integriteit te verifiëren.

## 1. Infrastructuur Opstarten

De gehele stack (PostgreSQL, MinIO en pgAdmin) wordt gestart middels Docker Compose. Dit zorgt voor een consistente omgeving waarin alle componenten direct met elkaar kunnen communiceren.

Commando:
docker-compose up -d

## 2. Object Storage Inrichten (MinIO)

De fysieke bestanden (blobs) worden opgeslagen in MinIO om de database te ontlasten en schaalbaarheid te garanderen.

1. Navigeer naar de MinIO Console via http://localhost:9001.
2. Log in met de administrator gegevens uit het .env bestand. (deze vind je ook terug in example.env)
3. Maak een nieuwe bucket aan met de naam: archief-scans.
4. Upload de volgende PDF-bestanden naar deze bucket:
   - 001 - Inleiding Docker Swarm.pdf
   - 002 - Vervolg Docker Swarm.pdf
   - 003 - Software architectuur en message queues.pdf
   - 004 - Software architectuur verantwoorden.pdf
   - 005 - Gelaagde stijl en C4-model.pdf

## 3. Database Initialisatie en Data Import (pgAdmin)

Navigeer naar pgAdmin via http://localhost:8080. Om de metadata te koppelen aan de zojuist geüploade bestanden in MinIO, voer je het volgende SQL-script uit:

```sql

-- Stap 1: Tabellen opschonen
TRUNCATE audit_trail, document_versions, documents RESTART IDENTITY CASCADE;

-- Stap 2: Document metadata registreren
INSERT INTO documents (id, title, original_filename) VALUES
(1, 'Inleiding Docker Swarm', '001 - Inleiding Docker Swarm.pdf'),
(2, 'Vervolg Docker Swarm', '002 - Vervolg Docker Swarm.pdf'),
(3, 'Software architectuur en message queues', '003 - Software architectuur en message queues.pdf'),
(4, 'Software architectuur verantwoorden', '004 - Software architectuur verantwoorden.pdf'),
(5, 'Gelaagde stijl en C4-model', '005 - Gelaagde stijl en C4-model.pdf');

-- Stap 3: Koppeling leggen naar de Blobs in MinIO
INSERT INTO document_versions (document_id, minio_key, version_number) VALUES
(1, 'archief-scans/001 - Inleiding Docker Swarm.pdf', 1),
(2, 'archief-scans/002 - Vervolg Docker Swarm.pdf', 1),
(3, 'archief-scans/003 - Software architectuur en message queues.pdf', 1),
(4, 'archief-scans/004 - Software architectuur verantwoorden.pdf', 1),
(5, 'archief-scans/005 - Gelaagde stijl en C4-model.pdf', 1);

-- Stap 4: Audit trail invullen
INSERT INTO audit_trail (action, document_id, user_id) VALUES
('INITIAL_UPLOAD', 1, 'NickTheArchivist'),
('INITIAL_UPLOAD', 2, 'NickTheArchivist'),
('INITIAL_UPLOAD', 3, 'NickTheArchivist'),
('INITIAL_UPLOAD', 4, 'NickTheArchivist'),
('INITIAL_UPLOAD', 5, 'NickTheArchivist');

```

## 4. Validatie en Demonstratie

Gebruik de volgende queries om de koppeling aan te tonen:

Overzicht van het digitale archief:

```sql
SELECT d.id, d.title, v.minio_key AS "opslag_pad", v.version_number
FROM documents d
JOIN document_versions v ON d.id = v.document_id;
```

Controleren van acties (Audit Trail):

```sql
SELECT action, document_id, timestamp
FROM audit_trail
ORDER BY timestamp DESC;
```

## 5. Troubleshooting

Tijdens de ontwikkeling is gebleken dat kolomnamen in het SQL-script exact moeten matchen met de database-initiatie (init.sql). Indien er een "column does not exist" fout optreedt, controleer dan of de tabelstructuur in PostgreSQL overeenkomt met de kolomnamen in de INSERT-statements.
