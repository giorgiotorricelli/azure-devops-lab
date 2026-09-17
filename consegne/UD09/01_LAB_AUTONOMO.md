# UD09 — Laboratorio Guidato: Report di Consegna

## 1. Verifiche Preliminari (Ambiente e Repository)

### Azure CLI
```bash
az login
az account show --query "{Subscription:name,User:user.name}" --output table
```
Esito: Autenticazione eseguita con successo. Sottoscrizione Azure attiva rilevata e collegata all'utente del corso.

GitHub CLI e Repository
```bash
gh auth status
cd "$LAB_REPO"
gh repo view --json nameWithOwner,url
git remote -v
git fetch
```
Esito: Account GitHub autenticato. Repository remoto personale raggiungibile via HTTPS/Git. git fetch completato senza errori.

Sistema Operativo (WSL2 / Linux)
```bash
uname -a
uname -m
git --version
curl --version
```
Architettura: x86_64 (pacchetto agent Linux x64 selezionato).

Dipendenze base: Git e cURL presenti e aggiornati.

2. Configurazione Azure DevOps Organization e Project
Organization Name: azdo-student-lab-01

URL Organization: https://dev.azure.com/azdo-student-lab-01

Azure Subscription Linked: Yes

Project Name: az900-az104-devops

Visibility: Private

Version Control: Git

Work Item Process: Agile

3. Mappatura Gruppi di Sicurezza (Permissions)
Project Administrators: Utenti con controllo completo sul progetto; possono gestire permessi, impostazioni di rete, service connection e configurazioni dei servizi.

Contributors: Sviluppatori e membri del team che possono leggere e modificare elementi di lavoro, creare ed eseguire pipeline, effettuare push e gestire il codice.

Readers: Utenti con permessi di sola lettura sulle risorse del progetto (non possono modificare impostazioni, codice o pipeline).

Build Administrators: Utenti o servizi preposti alla gestione e configurazione specifica delle risorse di build, agent pool e autorizzazioni delle pipeline.

4. Integrazione GitHub Readiness
GitHub Repository: mio-utente/azure-devops-lab

Repository Private: Yes

GitHub Auth Verified: Yes

Preferred Future Integration: Azure Pipelines GitHub App

5. Parallelismo e Quota Esecuzioni (Parallel Jobs)
Self-hosted Parallelism: 1 Free Concurrent Job (Private Projects)

Microsoft-hosted Status: MICROSOFT_HOSTED_READY

Billing Subscription Linked: Yes

Hosted Action: Ready (1 Job concorrente attivo, limite 1.800 min/mese)

6. Configurazione Agent Pool e Self-Hosted Agent
Dettagli Registrazione
Agent Pool Name: pool-ud09-wsl

Agent Name: wsl-ud09-student

Work Folder: _work

Directory Locale Agent: ~/azdo-agent/

Registrazione ed Esecuzione
```bash
mkdir -p "$HOME/azdo-agent"
cd "$HOME/azdo-agent"
# Download ed estrazione completati
./bin/installdependencies.sh
./config.sh --url [https://dev.azure.com/azdo-student-lab-01](https://dev.azure.com/azdo-student-lab-01) --auth pat --pool pool-ud09-wsl --agent wsl-ud09-student --acceptTeePluginLicense
./run.sh
```
7. Stato Agent e Revoca PAT
Stato Portale: Online

Versione Agent: 3.240.1

Azione sul PAT: PAT ud09-agent-registration revocato tramite la sezione Personal Access Tokens.

Stato dopo la revoca PAT: L'agent rimane stabilmente Online. Il PAT è servito unicamente al momento della registrazione iniziale.

8. Capability Rilevate dell'Agent
Dalla sezione Capabilities dell'agent nel portale:

Agent.OS: Linux

Agent.OSArchitecture: X64

Agent.Version: 3.240.1

git: /usr/bin/git

PATH: Contiene i percorsi di sistema di Ubuntu/WSL2 (/usr/local/sbin, /usr/local/bin, /usr/sbin, /usr/bin, ecc.)

python3: /usr/bin/python3

9. Test Ciclo di Vita (Online → Offline → Online)
Premuto Ctrl+C nel terminale in cui era in esecuzione ./run.sh.

Verificato aggiornamento stato su Azure DevOps: Agent risultato Offline.

Riavviato l'agent con ./run.sh.

Verificato ripristino stato su Azure DevOps: Agent ritornato Online senza necessità di rieseguire la configurazione o generare un nuovo PAT.

10. Checklist di Readiness Finale

Organization: azdo-student-lab-01
Project privato: az900-az104-devops
GitHub repository/integration readiness: mio-utente/azure-devops-lab (Verificato)
Microsoft-hosted status: MICROSOFT_HOSTED_READY
Self-hosted status: Configurato e Funzionante
Pool: pool-ud09-wsl
Agent: wsl-ud09-student
Agent Online: Yes
PAT revocato oppure Device Code Flow: PAT revocato correttamente
Capability essenziali: Agent.OS (Linux), git, python3, PATH registrati
Restart Offline/Online verificato: Yes
Nessun segreto nel repository: Yes