#include <iostream>
#include <string>
#include <map>
#include <cstdlib>
#include <cstring>
#include <unistd.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <netinet/in.h>

#include "Chat.h"

std::map<int, Chat> chatRooms; // Map для хранения комнат чата с их ID

int createChatRoom() {
    static int nextChatRoomId = 1;
    chatRooms[nextChatRoomId] = Chat();
    return nextChatRoomId++;
}

void processMessage(int chatRoomId, const std::string& sender, const std::string& recipient, const std::string& message) {
    Chat& chat = chatRooms[chatRoomId];
    chat.sendMessage(sender, recipient, message);
}

void processCommand(const std::string& command, int chatRoomId) {
    Chat& chat = chatRooms[chatRoomId];

    if (command == "/register") {
        // Запросить имя пользователя и пароль
        std::string username, password;
        std::cout << "Введите имя пользователя: ";
        std::cin >> username;
        std::cout << "Введите пароль: ";
        std::cin >> password;

        chat.registerUser(username, password);
    } else if (command == "/send") {
        // Запросить данные отправителя, получателя и сообщение
        std::string sender, recipient, message;
        std::cout << "Введите отправителя: ";
        std::cin >> sender;
        std::cout << "Введите получателя: ";
        std::cin >> recipient;
        std::cout << "Введите сообщение: ";
        std::cin.ignore();
        std::getline(std::cin, message);

        chat.sendMessage(sender, recipient, message);
    } else if (command == "/print") {
        // Запросите имя получателя для вывода сообщений
        std::string recipient;
        std::cout << "Введите имя получателя: ";
std::cin >> recipient;

    chat.printMessages(recipient);
} else {
    std::cout << "Неверная команда. Доступные команды: /register, /send, /print" << std::endl;
}
}

int main() {
int serverSocket, clientSocket;
socklen_t clientAddrLen;
struct sockaddr_in serverAddr, clientAddr;
char buffer[1024];

// Создание сокета
serverSocket = socket(AF_INET, SOCK_STREAM, 0);
if (serverSocket < 0) {
    perror("Ошибка при создании сокета");
    return 1;
}

// Настройка адреса сервера
serverAddr.sin_family = AF_INET;
serverAddr.sin_port = htons(12345); // Пример порта - 12345
serverAddr.sin_addr.s_addr = INADDR_ANY;

// Привязка сокета к адресу и порту
if (bind(serverSocket, (struct sockaddr*)&serverAddr, sizeof(serverAddr)) < 0) {
    perror("Ошибка при привязке сокета");
    return 1;
}

// Ожидание подключения клиента
listen(serverSocket, 1);
std::cout << "Сервер запущен и ожидает подключения клиентов..." << std::endl;

while (true) {
    // Принятие клиентского соединения
    clientAddrLen = sizeof(clientAddr);
    clientSocket = accept(serverSocket, (struct sockaddr*)&clientAddr, &clientAddrLen);
    if (clientSocket < 0) {
        perror("Ошибка при принятии клиентского соединения");
        return 1;
    }

    std::cout << "Подключился новый клиент" << std::endl;

    // Обработка команд от клиента
    while (true) {
        memset(buffer, 0, sizeof(buffer));
        ssize_t bytesRead = recv(clientSocket, buffer, sizeof(buffer), 0);
        if (bytesRead <= 0) {
            std::cout << "Клиент отключился" << std::endl;
            close(clientSocket);
            break;
        }

        std::string command(buffer);
        processCommand(command);

        // Отправка ответа клиенту
        std::string response = "Команда успешно выполнена";
        ssize_t bytesSent = send(clientSocket, response.c_str(), response.length(), 0);
        if (bytesSent < 0) {
            perror("Ошибка отправки данных клиенту");
            close(clientSocket);
            break;
        }
    }
}

close(serverSocket);

return 0;
}
