# Sprawozdanie z laboratorium DevOps

## 1. Wstęp

Celem laboratorium było zapoznanie się z narzędziami i praktykami DevOps poprzez stworzenie projektu, w którym zostanie zaimplementowany pełny pipeline DevOps. Projekt składa się z frontendu napisanego w Astro oraz backendu wykorzystującego Payload CMS z bazą danych MongoDB.

## 2. Linux

Podczas realizacji projektu wykorzystano następujące komendy Linux:
- ls # Wyświetla zawartość bieżącego katalogu
- cd etc # Przechodzi do katalogu /etc
- mkdir Lab1 # Tworzy nowy katalog o nazwie Lab1
- cd lab1 # Próba przejścia do katalogu lab1 (małe litery)
- cd Lab1 # Przejście do katalogu Lab1 (wielkie L)
- vim notatki.txt # Otwiera edytor vim z nowym plikiem notatki.txt
- cat notatki.txt # Wyświetla zawartość pliku notatki.txt
- ls -l notatki.txt # Wyświetla szczegółowe informacje o pliku notatki.txt
- chmod 666 notatki.txt # Zmienia uprawnienia pliku na rw-rw-rw- (odczyt i zapis dla wszystkich)
- ls -l notatki.txt # Ponowne wyświetlenie uprawnień po zmianie
- ls /etc > lista_plikow.txt # Zapisuje listę plików z /etc do pliku lista_plikow.txt
- cat lista_plikow.txt # Wyświetla zawartość pliku lista_plikow.txt
- ls /etc > lista_plików.txt # Próba zapisu z polskimi znakami (może powodować problemy)
- ls /etc > lista_plikow.txt # Poprawiona wersja bez polskich znaków
- cat lista_plikow.txt # Wyświetla zawartość poprawionego pliku
- find /etc -type f -name "*.conf" # Wyszukuje pliki .conf w katalogu /etc
- sudo apt update # Aktualizuje listę pakietów
- sudo apt install htop # Instaluje narzędzie htop do monitorowania systemu
- htop # Uruchamia htop

# Instalacja Docker:
- sudo apt-get update # Aktualizacja listy pakietów
- sudo apt-get install ca-certificates curl # Instalacja wymaganych certyfikatów i curl
- sudo install -m 0755 -d /etc/apt/keyrings # Utworzenie katalogu na klucze
- sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc # Pobranie klucza GPG Dockera
- sudo chmod a+r /etc/apt/keyrings/docker.asc # Nadanie uprawnień odczytu dla klucza

# Dodanie repozytorium Docker do źródeł apt:
- echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
 $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null # Dodanie repozytorium
- sudo apt-get update # Aktualizacja z nowym repozytorium
- sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin # Instalacja Dockera
- sudo docker run hello-world # Test instalacji Dockera
- docker run -it ubuntu bash # Próba uruchomienia kontenera Ubuntu

# Czyszczenie i ponowna instalacja Docker:
- for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done # Usunięcie starych wersji
- [Powtórzenie kroków instalacji Dockera jak wyżej]

# Sprawdzenie statusu i testy:
- sudo systemctl status docker # Sprawdzenie statusu usługi Docker (kilkukrotnie)
- docker run hello-world # Próby uruchomienia kontenera testowego
- docker run # Niepoprawna komenda (brak argumentów)
- sudo docker run hello-world # Uruchomienie z sudo
- history # Wyświetlenie historii komend

## 3. Git

Wykorzystane komendy Git:

git init # Inicjalizuje nowe repozytorium Git w bieżącym katalogu, tworząc ukryty katalog .git z metadanymi

git checkout -b feature/new-feature # Tworzy nową gałąź 'feature/new-feature' i przełącza się na nią. Jest to skrót dla 'git branch feature/new-feature' + 'git checkout feature/new-feature'

git add . # Dodaje wszystkie zmodyfikowane i nowe pliki z bieżącego katalogu do poczekalni (staging area). Kropka oznacza bieżący katalog i wszystkie podkatalogi

git commit -m "feat: new feature" # Tworzy nowy commit z plików w poczekalni. Flaga -m pozwala na dodanie wiadomości commita bezpośrednio w linii poleceń. Format "feat:" wskazuje na konwencję Conventional Commits

git push origin feature/new-feature # Wysyła commity z lokalnej gałęzi feature/new-feature do zdalnego repozytorium (origin) na tę samą gałąź

git status # Wyświetla stan repozytorium - pokazuje pliki zmodyfikowane, w poczekalni oraz te nieśledzone przez Git

git log # Wyświetla historię commitów w kolejności od najnowszego, pokazując hash commita, autora, datę i wiadomość

git branch # Wyświetla listę wszystkich lokalnych gałęzi, gwiazdką (*) oznaczona jest aktualnie wybrana gałąź

git merge feature/new-feature # Łączy zmiany z gałęzi feature/new-feature do aktualnie wybranej gałęzi (zazwyczaj main lub master)

### Generowanie i dodawanie klucza SSH:

1. Generowanie klucza:



2. Dodanie klucza do agenta SSH:

-   bash
-   eval $(ssh-agent -s)
-   ssh-add ~/.ssh/id_ed25519

3. Skopiowanie klucza publicznego i dodanie go w ustawieniach GitHub.

Klucze SSH są wykorzystywane do bezpiecznej autentykacji z serwerami Git bez konieczności podawania nazwy użytkownika i hasła przy każdej operacji. Zapewniają one wyższy poziom bezpieczeństwa niż autentykacja hasłem, ponieważ wykorzystują kryptografię asymetryczną.

## 4. Docker

Konteneryzacja to technologia umożliwiająca pakowanie aplikacji wraz ze wszystkimi jej zależnościami w izolowane środowisko (kontener), które może być uruchamiane w spójny sposób na różnych platformach.

Przykład Dockerfile dla aplikacji Astro:

```
//astro-project/Dockerfile

FROM node:lts AS build
WORKDIR /app
COPY . .
RUN npm i
RUN npm run build

FROM httpd:2.4 AS runtime
COPY --from=build /app/dist /usr/local/apache2/htdocs/
EXPOSE 80
```

Wyjaśnienie komend:

-   `FROM node:lts AS build` - używamy obrazu Node.js LTS jako bazowego dla etapu budowania
-   `WORKDIR /app` - ustawienie katalogu roboczego w kontenerze
-   `COPY . .` - kopiowanie plików projektu do kontenera
-   `RUN npm i && npm run build` - instalacja zależności i budowanie aplikacji
-   `FROM httpd:2.4 AS runtime` - używamy Apache jako serwera dla produkcji
-   `COPY --from=build /app/dist /usr/local/apache2/htdocs/` - kopiowanie zbudowanej aplikacji do katalogu serwera
-   `EXPOSE 80` - eksponowanie portu 80 dla kontenera

Przykład Dockerfile dla aplikacji Payload CMS:

```
//payload/Dockerfile

# From https://github.com/vercel/next.js/blob/canary/examples/with-docker/Dockerfile

FROM node:18-alpine AS base

# Install dependencies only when needed
FROM base AS deps
# Check https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine to understand why libc6-compat might be needed.
RUN apk add --no-cache libc6-compat
WORKDIR /app

# Install dependencies based on the preferred package manager
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./
RUN \
  if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
  elif [ -f package-lock.json ]; then npm ci; \
  elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm i --frozen-lockfile; \
  else echo "Lockfile not found." && exit 1; \
  fi


# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Next.js collects completely anonymous telemetry data about general usage.
# Learn more here: https://nextjs.org/telemetry
# Uncomment the following line in case you want to disable telemetry during the build.
# ENV NEXT_TELEMETRY_DISABLED 1

RUN \
  if [ -f yarn.lock ]; then yarn run build; \
  elif [ -f package-lock.json ]; then npm run build; \
  elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm run build; \
  else echo "Lockfile not found." && exit 1; \
  fi

# Production image, copy all the files and run next
FROM base AS runner
WORKDIR /app

ENV NODE_ENV production
# Uncomment the following line in case you want to disable telemetry during runtime.
# ENV NEXT_TELEMETRY_DISABLED 1

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public

# Set the correct permission for prerender cache
RUN mkdir .next
RUN chown nextjs:nodejs .next

# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000

# server.js is created by next build from the standalone output
# https://nextjs.org/docs/pages/api-reference/next-config-js/output
CMD HOSTNAME="0.0.0.0" node server.js

```

Wyjaśnienie komend:

-   `FROM node:18-alpine AS base` - używamy obrazu Node.js LTS jako bazowego dla etapu budowania
-   `COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* ./` - kopiowanie plików konfiguracyjnych
-   `RUN \ if [ -f yarn.lock ]; then yarn --frozen-lockfile; \ elif [ -f package-lock.json ]; then npm ci; \ elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm i --frozen-lockfile; \ else echo "Lockfile not found." && exit 1; \ fi` - instalacja zależności
-   `FROM base AS builder` - używamy obrazu Node.js LTS jako bazowego dla etapu budowania
-   `WORKDIR /app` - ustawienie katalogu roboczego w kontenerze
-   `COPY --from=deps /app/node_modules ./node_modules` - kopiowanie zbudowanych zależności
-   `COPY . .` - kopiowanie plików projektu do kontenera
-   `FROM base AS runner` - używamy obrazu Node.js LTS jako bazowego dla etapu budowania
-   `WORKDIR /app` - ustawienie katalogu roboczego w kontenerze
-   `ENV NODE_ENV production` - ustawienie zmiennej środowiskowej NODE_ENV na production
-   `RUN addgroup --system --gid 1001 nodejs` - tworzenie grupy nodejs
-   `RUN adduser --system --uid 1001 nextjs` - tworzenie użytkownika nextjs
-   `COPY --from=builder /app/public ./public` - kopiowanie plików publicznych
-   `RUN mkdir .next` - tworzenie katalogu .next
-   `RUN chown nextjs:nodejs .next` - ustawienie właściciela katalogu .next
-   `COPY --from=builder /app/public ./public` - kopiowanie plików publicznych
-   `RUN mkdir .next` - tworzenie katalogu .next
-   `chown nextjs:nodejs .next` - ustawienie właściciela katalogu .next
-   `COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./` - kopiowanie zbudowanej aplikacji do katalogu serwera
-   `COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static` - kopiowanie statycznych plików do katalogu serwera
-   `USER nextjs` - ustawienie użytkownika nextjs
-   `EXPOSE 3000` - eksponowanie portu 3000 dla kontenera
-   `ENV PORT 3000` - ustawienie portu 3000 jako domyślnego portu dla kontenera
-   `CMD HOSTNAME="0.0.0.0" node server.js` - uruchomienie serwera

## 5. Docker Compose

Docker Compose to narzędzie do definiowania i uruchamiania wielokontenerowych aplikacji Docker. Pozwala na konfigurację wszystkich usług aplikacji w jednym pliku YAML.

Przykład docker-compose.yml dla Payload CMS:

```
//payload/docker-compose.yml

version: '3'

services:
  payload:
    image: node:18-alpine
    ports:
      - '3000:3000'
    volumes:
      - .:/home/node/app
      - node_modules:/home/node/app/node_modules
    working_dir: /home/node/app/
    command: sh -c "corepack enable && corepack prepare pnpm@latest --activate && pnpm install && pnpm dev"
    depends_on:
      - mongo
    env_file:
      - .env

  mongo:
    image: mongo:latest
    ports:
      - '27017:27017'
    command:
      - --storageEngine=wiredTiger
    volumes:
      - data:/data/db
    logging:
      driver: none

volumes:
  data:
  node_modules:

```

Wyjaśnienie sekcji:

-   `services` - definicje poszczególnych usług, które składają się na aplikację. W tym przypadku mamy dwie usługi: Payload CMS (aplikacja) oraz MongoDB (baza danych). Każda usługa jest uruchamiana w osobnym kontenerze.

-   `payload` - konfiguracja aplikacji Payload CMS, która zawiera:

    -   obraz bazowy node:18-alpine
    -   mapowanie portu 3000 kontenera na port 3000 hosta
    -   montowanie wolumenów dla kodu aplikacji i modułów node
    -   ustawienie katalogu roboczego
    -   polecenie uruchamiające aplikację w trybie deweloperskim
    -   zależność od usługi mongo
    -   wczytywanie zmiennych środowiskowych z pliku .env

-   `mongo` - konfiguracja bazy danych MongoDB, która obejmuje:

    -   najnowszy oficjalny obraz MongoDB
    -   mapowanie portu 27017 dla dostępu do bazy
    -   użycie silnika WiredTiger do przechowywania danych
    -   montowanie wolumenu dla persystencji danych
    -   wyłączenie logowania dla zachowania przejrzystości

-   `volumes` - definicje wolumenów używanych przez kontenery:
    -   data: przechowuje dane MongoDB między restartami kontenerów
    -   node_modules: przechowuje zainstalowane zależności Node.js, co przyspiesza kolejne uruchomienia

## 6. CI/CD z GitHub Actions

CI (Continuous Integration) to praktyka automatycznego integrowania zmian w kodzie poprzez budowanie i testowanie przy każdym push'u do repozytorium.

Przykład workflow:

```
//.github/workflows/astro-deploy.yml

name: Deploy Astro site

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

# Add these permissions
permissions:
  contents: write
  pages: write
  id-token: write

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: '20'
        cache: 'npm'
        cache-dependency-path: './astro-project/package.json'

    - name: Install dependencies
      working-directory: ./astro-project
      run: npm install

    - name: Build site
      working-directory: ./astro-project
      run: npm run build

    - name: Deploy to GitHub Pages
      if: github.ref == 'refs/heads/main'
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./astro-project/dist
```

Workflow ten realizuje następujące zadania:

1. Uruchamia się automatycznie przy każdym push'u i pull requeście do gałęzi main
2. Wykorzystuje runner'a z Ubuntu jako środowisko wykonawcze
3. Wykonuje kolejno kroki:
    - Pobiera kod z repozytorium (checkout)
    - Konfiguruje środowisko Node.js w wersji 20 z cache'owaniem zależności
    - Instaluje zależności projektu (npm install)
    - Buduje aplikację (npm run build)
    - Jeśli zmiany są w gałęzi main, deployuje zbudowaną aplikację na GitHub Pages

Dzięki temu workflow, każda zmiana w kodzie jest automatycznie weryfikowana poprzez proces budowania, a zatwierdzone zmiany w głównej gałęzi są od razu publikowane na środowisku produkcyjnym (GitHub Pages). Eliminuje to potrzebę ręcznego deploymentu i zapewnia, że na produkcji znajduje się zawsze działająca wersja aplikacji.

Warto zwrócić uwagę na konfigurację uprawnień (permissions), która jest niezbędna do prawidłowego działania GitHub Pages oraz cache'owanie zależności npm, które przyspiesza proces budowania poprzez zachowanie wcześniej pobranych pakietów.

Przykład workflow dla budowania obrazów Docker:

```
//.github/workflows/docker-image.yml

name: Docker Image CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build-payload:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3

    - name: Build Payload Docker image
      working-directory: ./payload
      run: |
        docker build . --file Dockerfile --tag payload:$(date +%s)

  build-astro:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v3

    - name: Build Astro Docker image
      working-directory: ./astro-project
      run: |
        docker build . --file Dockerfile --tag astro:$(date +%s)
```

Workflow ten realizuje następujące zadania:

1. Uruchamia się automatycznie przy każdym push'u i pull requeście do gałęzi main
2. Wykorzystuje runner'a z Ubuntu jako środowisko wykonawcze
3. Wykonuje kolejno kroki:
    - Pobiera kod z repozytorium (checkout)
    - Konfiguruje środowisko Docker Buildx
    - Buduje obraz Docker dla aplikacji Astro
    - Buduje obraz Docker dla aplikacji Payload CMS

## 7. Kubernetes

Kubernetes to platforma do orkiestracji kontenerów, która automatyzuje wdrażanie, skalowanie i zarządzanie aplikacjami kontenerowymi.

Przykład konfiguracji deploymentu:

```
//k8s/astro-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: astro-frontend
  labels:
    app: astro-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: astro-frontend
  template:
    metadata:
      labels:
        app: astro-frontend
    spec:
      containers:
      - name: astro
        image: astro:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            memory: "512Mi"
            cpu: "500m"
          requests:
            memory: "256Mi"
            cpu: "250m"
```

Konfiguracja zawiera:

1. Metadane:

    - Nazwa deploymentu (astro-frontend)
    - Etykiety identyfikujące aplikację (app: astro-frontend)

2. Specyfikację (spec):

    - Liczba replik (replicas: 2) - określa ile kopii aplikacji ma działać
    - Selektor (selector) - określa które pody są zarządzane przez deployment
    - Szablon (template) - definiuje jak mają wyglądać pody

3. Konfigurację kontenera:
    - Nazwa i obraz kontenera (astro:latest)
    - Port kontenera (80)
    - Limity zasobów:
        - Pamięć: limit 512Mi, żądanie 256Mi
        - CPU: limit 500m, żądanie 250m

Pozostałe komponenty Kubernetes w projekcie:

1. Serwisy (Services):

    - astro-frontend-service (LoadBalancer) - udostępnia aplikację na zewnątrz
    - mongo-service (ClusterIP) - wewnętrzny dostęp do bazy MongoDB
    - payload-service (ClusterIP) - wewnętrzny dostęp do Payload CMS

2. Deployment bazy danych MongoDB:
    - Pojedyncza replika
    - Persistent Volume Claim dla trwałego przechowywania danych
    - Kontener MongoDB z zamontowanym wolumenem

## 8. Wnioski

DevOps jest niezbędnym elementem nowoczesnego procesu wytwarzania oprogramowania. Główne korzyści:

1. Automatyzacja procesów - eliminacja powtarzalnych zadań i redukcja błędów ludzkich
2. Szybsze wdrożenia - ciągła integracja i deployment przyspieszają dostarczanie nowych funkcji
3. Lepsza współpraca - zespoły dev i ops pracują razem, co prowadzi do lepszej komunikacji
4. Skalowalność - łatwiejsze zarządzanie infrastrukturą i skalowanie aplikacji
5. Monitoring i bezpieczeństwo - lepsze śledzenie wydajności i szybsze reagowanie na problemy

Zastosowanie praktyk DevOps w tym projekcie znacząco usprawniło proces rozwoju i wdrażania aplikacji, pokazując jak ważne jest zautomatyzowane podejście do cyklu życia oprogramowania.
