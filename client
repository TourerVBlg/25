#include <iostream>
#include <string>
#include <cstring>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

int main() {
    int clientSocket;
    struct sockaddr_in serverAddr;
    char buffer[1024];

    // Создание сокета
    clientSocket = socket(AF_INET, SOCK_STREAM, 0);
    if (clientSocket < 0) {
        perror("Ошибка при создании сокета");
        return 1;
    }

    // Настройка адреса сервера
    serverAddr.sin_family = AF_INET;
    serverAddr.sin_port = htons(12345); // Пример порта - 12345
    if(inet_pton(AF_INET, "127.0.0.1", &(serverAddr.sin_addr)) <= 0){
        perror("Ошибка при настройке адреса сервера");
        return 1;
    }

    // Подключение к серверу
    if (connect(clientSocket, (struct sockaddr*)&serverAddr, sizeof(serverAddr)) < 0) {
        perror("Ошибка при подключении к серверу");
        return 1;
    }

    std::cout << "Подключение к серверу успешно" << std::endl;

    while (true) {
        // Запрос команды у пользователя
        std::string command;
        std::cout << "Введите команду: ";
        std::getline(std::cin, command);

        // Отправка команды на сервер
        ssize_t bytesSent = send(clientSocket, command.c_str(), command.length(), 0);
        if (bytesSent < 0) {
            perror("Ошибка отправки данных на сервер");
            close(clientSocket);
            return 1;
        }

        // Получение и вывод ответа от сервера
        memset(buffer, 0, sizeof(buffer));
        ssize_t bytesRead = recv(clientSocket, buffer, sizeof(buffer), 0);
        if (bytesRead <= 0) {
            std::cout << "Соединение с сервером разорвано" << std::endl;
            close(clientSocket);
            return 1;
        }
        
        std::cout << "Ответ от сервера: " << buffer << std::endl;
    }

    close(clientSocket);

    return 0;
}
