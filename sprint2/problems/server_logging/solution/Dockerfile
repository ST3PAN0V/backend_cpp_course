FROM gcc:11.3 as build

RUN apt update && \
    apt install -y \
      python3-pip \
      cmake \
    && \
    pip install prometheus_client && \
    pip3 install conan==1.*

COPY conanfile.txt /app/
RUN mkdir /app/build && cd /app/build && \
    conan install .. -s compiler.libcxx=libstdc++11 --build=missing
COPY ./src /app/src
COPY CMakeLists.txt /app/

RUN cd /app/build && \
    cmake .. && \
    cmake --build .

FROM ubuntu:22.04 as run

RUN groupadd -r www && useradd -r -g www www
USER www

COPY --from=build /app/build/bin/game_server /app/
COPY ./data /app/data
COPY ./static /app/static

ENTRYPOINT ["/app/game_server", "/app/data/config.json", "app/static"]
