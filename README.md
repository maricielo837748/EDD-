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
// Clase que representa un planificador de CPU basado en una cola de prioridad.
// Utiliza una lista enlazada para mantener los procesos ordenados por prioridad.
class PlanificadorCPU {
private:
    Nodo* frente; // Puntero al primer nodo de la cola (proceso con mayor prioridad)

public:
    // Constructor: inicializa la cola vacía
    PlanificadorCPU() {
        frente = NULL;
    }

    // Método para encolar un proceso en la cola de prioridad.
    // Los procesos con mayor prioridad (valor numérico más alto) se colocan al frente.
    void encolar(Proceso p) {
        // Se crea un nuevo nodo con una copia del proceso recibido.
        Nodo* nuevo = new Nodo(new Proceso(p.id, p.nombre, p.prioridad));

        // Si la cola está vacía o el nuevo proceso tiene mayor prioridad que el primero:
        if (frente == NULL || nuevo->proceso->prioridad > frente->proceso->prioridad) {
            nuevo->siguiente = frente;
            frente = nuevo;
        } else {
            // Buscar la posición adecuada en la cola (por prioridad)
            Nodo* temp = frente;
            while (temp->siguiente != NULL && temp->siguiente->proceso->prioridad >= nuevo->proceso->prioridad) {
                temp = temp->siguiente;
            }
            // Insertar el nuevo nodo en la posición encontrada
            nuevo->siguiente = temp->siguiente;
            temp->siguiente = nuevo;
        }

        // Commit: Maricielo - Implementación del encolado de procesos en la cola de prioridad
    }

    // Método para desencolar (eliminar) el proceso con mayor prioridad (el del frente).
    void desencolar() {
        if (frente == NULL) {
            cout << "La cola está vacía." << endl;
            return;
        }

        // Eliminar el nodo del frente y mostrar sus datos
        Nodo* temp = frente;
        frente = frente->siguiente;

        cout << "Ejecutando proceso ID: " << temp->proceso->id
             << ", Nombre: " << temp->proceso->nombre << endl;

        delete temp;

        // Commit: Maricielo - Implementación de desencolado de proceso para ejecución
    }

    // Método para mostrar todos los procesos en la cola, en orden de prioridad.
    void mostrar_cola() {
        Nodo* temp = frente;
        while (temp != NULL) {
            cout << "ID: " << temp->proceso->id
                 << ", Nombre: " << temp->proceso->nombre
                 << ", Prioridad: " << temp->proceso->prioridad << endl;
            temp = temp->siguiente;
        }
    }
};
