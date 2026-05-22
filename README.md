name: Build Sculk Spread Mod

# Kiedy uruchamiać build:
on:
  push:                  # Przy każdym wrzuceniu zmian
    branches: [ main, master ]
  workflow_dispatch:     # I ręcznie z zakładki Actions w GitHub

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read

    steps:
      # 1. Pobranie kodu z repo
      - name: Checkout
        uses: actions/checkout@v4

      # 2. Instalacja JDK 21 w maszynie buildującej (Temurin)
      - name: Set up JDK 21
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 21

      # 3. Wygenerowanie Gradle Wrapper (bo nie ma go w repo)
      - name: Generate Gradle wrapper
        run: |
          gradle wrapper --gradle-version 8.10
          chmod +x gradlew

      # 4. Build moda
      - name: Build mod
        run: ./gradlew build --no-daemon --stacktrace

      # 5. Upload gotowego .jar jako Artifact (do pobrania z UI)
      - name: Upload mod jar
        uses: actions/upload-artifact@v4
        with:
          name: sculkspread-mod
          path: build/libs/*.jar
          if-no-files-found: error
          retention-days: 30
