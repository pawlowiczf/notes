## Podstawowe komendy dot. zarządzania użytkownikami na Linux
```bash
# Tworzenie nowego użytkownika
sudo adduser root 

# Dodanie użytkownika do grupy sudo 
sudo usermod -aG sudo root 

# Przełączanie na nowego użytkownika 
su - root 

# Zmiana hasła użytkownika 
sudo passwd root 
```

## Instalacja 
Zawsze najpierw zainstaluj SDK Java, następnie Jenkinsa. Instrukcja krok po kroku: `https://www.jenkins.io/doc/book/installing/linux/`

## Instalacja na lekkich obrazach w Docker 
```sh
docker network create --driver bridge --subnet 172.25.0.0/24 jenkins-network 

docker run -dit --name jenkins-server-1 --network jenkins-network --ip 172.25.0.2 -p 2222:22 ubuntu:22.04
docker run -dit --name jenkins-agent-1  --network jenkins-network --ip 172.25.0.3 -p 2223:22 ubuntu:22.04

docker exec -it jenkins-server-1 bash
    apt update
    apt install openssh-server
    service ssh start
    passwd

ssh root@localhost -p 2222
```

## Konfiguracja agenta i połączenia przez SSH
Konfiguracja agenta i połączenie przez SSH (wraz z walidacją `known hosts`) składa się z kilku kroków, które trzeba wykonywać na odpowiednich maszynach!

**Na agencie:**

1. Stwórz w katalogu głównym (domyślnie `/home/ubuntu`), katalog roboczy dla agenta np. `/jenkins-agent`. 
2. Przejdź do folderu `/home/ubuntu/.ssh`. 
3. Wygeneruj klucz publiczny i prywatny do komunikacji SSH komendą: `ssh-keygen -t rsa -m PEM -f "jenkins_agent_rsa"`
4. Dodaj klucz publiczny do pliku `authorized_keys`, które znajduje się w tym samym folderze, co wygenerowane klucze. Można to zrobić poleceniem `echo` i operatorem `>>`. 

**Na głównym serwerze:**

1. Przejdź do folderu `/var/lib/jenkins` - jest to katalog serwisu Jenkins. Stwórz w tym katalogu folder `.ssh` oraz plik `known_hosts`. 
2. Wykonaj polecenie `sudo sh -c 'ssh-keyscan -H 3.127.107.212 >> /var/lib/jenkins/.ssh/known_hosts'`
3. W Jenkins UI dodaj credential SSH, wklej tam, wygenerowany na agencie, klucz prywatny. Dodaj nowego agenta, podając adres IP i konfigurację. 

## Materiały

`https://docs.dman.cloud/tutorial-documentation/install-jenkins/`

