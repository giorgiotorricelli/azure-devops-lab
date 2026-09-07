La distribuzione Linux in uso è **Ubuntu** operante su **WSL 2**, come verificato tramite la CLI di Windows (`wsl --list --verbose`).

Il repository locale risiede nel percorso Linux `/home/giorgio-wsl/workspace/azure-devops-lab` (verificato con `git rev-parse --show-toplevel`) ed è collegato al remote GitHub `https://github.com/giorgiotorricelli/corso-azure-devops.git` (`git remote -v`). L'invito di collaborazione inviato al docente risulta attualmente in stato *In attesa / Pendente*.

## Concetti Git e Architettura
* **Working Tree:** La cartella di lavoro locale contenente i file attualmente in fase di modifica.
* **Staging Area:** L'area d'aspetto temporanea in cui vengono preparati i file (tramite `git add`) destinati al prossimo commit.
* **Commit Locale:** La registrazione permanente nello storico locale dei file presenti nella Staging Area.
* **Repository Remoto:** La versione del progetto ospitata e sincronizzata su GitHub (`git push`).

## Diagnostica e Verifiche
* **Azure CLI:** La corretta installazione di Azure CLI è stata verificata tramite il comando `az --version` (versione rilevata: 2.90.0).
* **Errore di Contesto:** Un tipico errore di contesto si verifica eseguendo comandi Git al di fuori di un repository (`fatal: not a git repository`). Questo stato è diagnosticabile ricercando la presenza della cartella `.git` tramite il comando:
  `find ~/workspace -maxdepth 3 -type d -name .git -print`
EOF