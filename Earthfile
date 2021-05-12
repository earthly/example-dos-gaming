
jsdos:
    FROM alpine:3.13
    
    WORKDIR jsdos
    RUN wget https://js-dos.com/6.22/current/js-dos.js && \
        wget https://js-dos.com/6.22/current/wdosbox.js && \
        wget https://js-dos.com/6.22/current/wdosbox.wasm.js
    
    WORKDIR /
    SAVE ARTIFACT jsdos

serve:
    FROM node:16-alpine
    RUN npm install -g serve

darkhttpd:
    FROM alpine:3.13

    RUN apk add gcc musl-dev && \
        wget -O darkhttpd.c https://raw.githubusercontent.com/emikulic/darkhttpd/master/darkhttpd.c && \
        cc -static -Os -o darkhttpd darkhttpd.c

    SAVE ARTIFACT darkhttpd

game:
    FROM alpine:3.13
    
    ARG GAME_URL
    RUN wget -O game.zip $GAME_URL

    SAVE ARTIFACT game.zip

web-index:
    FROM alpine:3.13

    ARG GAME_ARGS

    COPY index.html .
    RUN sed -i s/GAME_ARGS/$GAME_ARGS/ index.html

    SAVE ARTIFACT index.html

web-serve:
    FROM +serve
    
    WORKDIR site
    COPY +game/game.zip .
    COPY +jsdos/jsdos .
    COPY +web-index/index.html .

    ENTRYPOINT npx serve -l tcp://0.0.0.0:8000

web-darkhttpd:
    FROM scratch

    ARG GAME_ARGS

    COPY +darkhttpd/darkhttpd .

    WORKDIR site
    COPY +game/game.zip .
    COPY +jsdos/jsdos .
    COPY +web-index/index.html .

    ENTRYPOINT ["/darkhttpd", "/site", "--port", "8000"]


play:
    LOCALLY

    ARG GAME_TAG
    WITH DOCKER --load jsdos:$GAME_TAG=+web-serve
        RUN docker inspect jsdos:$GAME_TAG > /dev/null && \ #Using side-effect to save image locally too
            docker run --rm -p 127.0.0.1:8000:8000 jsdos:$GAME_TAG
    END

play-dark:
    LOCALLY

    ARG GAME_TAG
    WITH DOCKER --load jsdos:$GAME_TAG=+web-darkhttpd
        RUN docker inspect jsdos:$GAME_TAG > /dev/null && \ #Using side-effect to save image locally too
            docker run --rm -p 127.0.0.1:8000:8000 jsdos:$GAME_TAG
    END

secretagent:
    BUILD \
        --build-arg GAME_TAG=secretagent \
        --build-arg GAME_URL=https://archive.org/download/SecretAgent_945/AGENT.ZIP \
        --build-arg GAME_ARGS=\"SAM1.EXE\" \
        +play-dark

cosmo:
    BUILD \
        --build-arg GAME_TAG=cosmo \
        --build-arg GAME_URL=https://archive.org/download/CosmosCosmicAdventure/CosmosCosmicAdventure-ForbiddenPlanet-Adventure1Of3V1.20sw1992apogeeSoftwareLtd.action.zip \
        --build-arg GAME_ARGS=\"COSMO1.EXE\" \
        +play-dark

doom:
    BUILD \
        --build-arg GAME_TAG=doom \
        --build-arg GAME_URL=https://archive.org/download/DoomsharewareEpisode/doom.ZIP \
        --build-arg GAME_ARGS=\"DOOM.EXE\" \
        +play-dark

scorch:
    BUILD \
        --build-arg GAME_TAG=scorch \
        --build-arg GAME_URL=https://archive.org/download/msdos_festival_SCORCH15/SCORCH15.ZIP \
        --build-arg GAME_ARGS=\"SCORCH.EXE\" \
        +play-dark

keen:
    BUILD \
        --build-arg GAME_TAG=keen \
        --build-arg GAME_URL=https://image.dosgamesarchive.com/games/keen-shr.zip \
        --build-arg GAME_ARGS=\"KEEN.BAT\" \
        +play-dark
