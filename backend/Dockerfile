FROM gcc:latest

WORKDIR /app

COPY app.cpp .

RUN g++ app.cpp -o app

CMD ["./app"]