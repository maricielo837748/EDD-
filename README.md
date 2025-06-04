#include <iostream>
#include <string>
using namespace std;

// Clase Proceso
// Esta clase representa a un proceso en el sistema. Cada proceso tiene un ID, un nombre,
// una prioridad, y un estado inicial de "Listo".
class Proceso {
public:
    int id;           // Identificador único del proceso
    string nombre;    // Nombre del proceso
    int prioridad;    // Prioridad del proceso (se usa para planificar su ejecución)
    string estado;    // Estado del proceso (por ejemplo, "Listo", "Ejecutando", etc.)

    // Constructor de la clase Proceso
    // Inicializa los atributos de la clase con los valores proporcionados
    Proceso(int id, string nombre, int prioridad) {
        this->id = id;
        this->nombre = nombre;
        this->prioridad = prioridad;
        this->estado = "Listo";  // Estado inicial es "Listo"
    }
};

// Nodo para lista enlazada
class Nodo {
public:
    Proceso* proceso;  // Puntero al objeto Proceso
    Nodo* siguiente;   // Puntero al siguiente nodo en la lista

    // Constructor de la clase Nodo
    Nodo(Proceso* p) {
        proceso = p;
        siguiente = NULL; // Inicialmente, no hay siguiente nodo
    }
};

// Lista enlazada - Gestor de procesos
class ListaEnlazada {
private:
    Nodo* cabeza; // Puntero al primer nodo de la lista

public:
    // Constructor de la clase ListaEnlazada
    ListaEnlazada() {
        cabeza = NULL; // Inicialmente, la lista está vacía
    }

    // Método para insertar un proceso en la lista
    void insertar(Proceso* p) {
        Nodo* nuevo = new Nodo(p);
        if (cabeza == NULL) {
            cabeza = nuevo;
        } else {
            Nodo* temp = cabeza;
            while (temp->siguiente != NULL) {
                temp = temp->siguiente;
            }
            temp->siguiente = nuevo;
        }
        cout << "Proceso agregado correctamente." << endl;
    }

    // Método para eliminar un proceso por su ID
    void eliminar(int id) {
        Nodo* temp = cabeza;
        Nodo* anterior = NULL;

        while (temp != NULL && temp->proceso->id != id) {
            anterior = temp;
            temp = temp->siguiente;
        }

        if (temp == NULL) {
            cout << "Proceso no encontrado." << endl;
            return;
        }

        if (anterior == NULL) {
            cabeza = temp->siguiente;
        } else {
            anterior->siguiente = temp->siguiente;
        }

        delete temp;
        cout << "Proceso eliminado correctamente." << endl;
    }

    // Método para buscar un proceso por su ID
    Proceso* buscar(int id) {
        Nodo* temp = cabeza;
        while (temp != NULL) {
            if (temp->proceso->id == id)
                return temp->proceso;
            temp = temp->siguiente;
        }
        return NULL;
    }

    // Método para modificar la prioridad de un proceso
    void modificar_prioridad(int id, int nueva_prioridad) {
        Proceso* p = buscar(id);
        if (p != NULL) {
            p->prioridad = nueva_prioridad;
            cout << "Prioridad modificada correctamente." << endl;
        } else {
            cout << "Proceso no encontrado." << endl;
        }
    }

    // Método para mostrar todos los procesos en la lista
    void mostrar() {
        Nodo* temp = cabeza;
        while (temp != NULL) {
            cout << "ID: " << temp->proceso->id
                 << ", Nombre: " << temp->proceso->nombre
                 << ", Prioridad: " << temp->proceso->prioridad
                 << ", Estado: " << temp->proceso->estado << endl;
            temp = temp->siguiente;
        }
    }
};

