
jsdos:
    FROM node:16-alpine
    WORKDIR site
    RUN wget https://js-dos.com/6.22/current/js-dos.js && \
        wget https://js-dos.com/6.22/current/wdosbox.js && \
        wget https://js-dos.com/6.22/current/wdosbox.wasm.js
    RUN npm install -g serve

game:
    FROM +jsdos
    ARG GAME_URL
    RUN wget -O game.zip $GAME_URL

web:
    FROM +game

    ARG GAME_ARGS

    COPY index.html .
    RUN sed -i s/GAME_ARGS/$GAME_ARGS/ index.html

    ENTRYPOINT npx serve -l tcp://0.0.0.0:8000

play:
    LOCALLY

    ARG GAME_TAG

    WITH DOCKER --load jsdos:$GAME_TAG=+web
        RUN docker inspect jsdos:$GAME_TAG > /dev/null && \ #Using side-effect to save image locally too
            docker run --rm -p 127.0.0.1:8000:8000 jsdos:$GAME_TAG
    END


secretagent:
    BUILD \
        --build-arg GAME_TAG=secretagent \
        --build-arg GAME_URL=https://archive.org/download/SecretAgent_945/AGENT.ZIP \
        --build-arg GAME_ARGS=\"SAM1.EXE\" \
        +play

cosmo:
    BUILD \
        --build-arg GAME_TAG=cosmo \
        --build-arg GAME_URL=https://archive.org/download/CosmosCosmicAdventure/CosmosCosmicAdventure-ForbiddenPlanet-Adventure1Of3V1.20sw1992apogeeSoftwareLtd.action.zip \
        --build-arg GAME_ARGS=\"COSMO1.EXE\" \
        +play

doom:
    ARG EARTHLY_TARGET
    BUILD \
        --build-arg GAME_TAG=doom \
        --build-arg GAME_URL=https://archive.org/download/DoomsharewareEpisode/doom.ZIP \
        --build-arg GAME_ARGS=\"DOOM.EXE\" \
        +play