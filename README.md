#include <iostream>
#include <string>
using namespace std;

// Estructura del proceso
struct Proceso {
    int id;                    // Identificador único del proceso
    string nombre;             // Nombre del proceso
    int prioridad;             // Nivel de prioridad del proceso
    Proceso* siguiente;        // Puntero al siguiente proceso en la lista
};


// Lista enlazada para gestor de procesos
class ListaProcesos {
    Proceso* cabeza;           // Puntero al primer proceso de la lista
public:
    // Constructor que inicializa la lista vacía
    ListaProcesos() : cabeza(NULL) {}


        // Inserta un nuevo proceso al final de la lista
    void insertarProceso(int id, string nombre, int prioridad) {
        Proceso* nuevo = new Proceso;           // Se crea un nuevo nodo Proceso en memoria dinámica
        nuevo->id = id;                         // Se asigna el ID al nuevo proceso
        nuevo->nombre = nombre;                 // Se asigna el nombre
        nuevo->prioridad = prioridad;           // Se asigna la prioridad
        nuevo->siguiente = NULL;                // El puntero siguiente se inicializa como NULL (último elemento)
        if (!cabeza) {                          // Si la lista está vacía
            cabeza = nuevo;                     // El nuevo proceso se convierte en el primero
        } else {
            Proceso* actual = cabeza;           // Se recorre desde el principio
            while (actual->siguiente)           // Mientras no se llegue al último nodo
                actual = actual->siguiente;     // Avanza al siguiente nodo
            actual->siguiente = nuevo;          // Se enlaza el nuevo proceso al final
        }
        cout << "Proceso insertado correctamente.\n";  // Mensaje de éxito
    }


        // Muestra todos los procesos registrados en la lista
    void mostrarProcesos() {
        if (!cabeza) {                          // Si la lista está vacía
            cout << "No hay procesos registrados.\n";
            return;
        }
        Proceso* actual = cabeza;               // Se empieza desde el primer nodo
        cout << "Procesos registrados:\n";
        while (actual) {                        // Mientras haya nodos en la lista
            // Se imprime la información del proceso actual
            cout << "ID: " << actual->id << " | Nombre: " << actual->nombre << " | Prioridad: " << actual->prioridad << "\n";
            actual = actual->siguiente;         // Avanza al siguiente nodo
        }
    }


        // Busca un proceso por ID y retorna su puntero si lo encuentra
    Proceso* buscarPorID(int id) {
        Proceso* actual = cabeza;               // Se empieza desde la cabeza
        while (actual) {                        // Mientras haya procesos
            if (actual->id == id) return actual; // Si el ID coincide, retorna el puntero al proceso
            actual = actual->siguiente;         // Avanza al siguiente
        }
        return NULL;                            // Si no se encuentra, retorna NULL
    }


        // Busca un proceso por nombre y retorna su puntero si lo encuentra
    Proceso* buscarPorNombre(const string& nombre) {
        Proceso* actual = cabeza;               // Comienza desde la cabeza
        while (actual) {
            if (actual->nombre == nombre)       // Si el nombre coincide
                return actual;                  // Retorna el proceso
            actual = actual->siguiente;         // Avanza al siguiente nodo
        }
        return NULL;                            // Si no lo encuentra, retorna NULL
    }


        // Elimina un proceso de la lista por su ID
    void eliminarProceso(int id) {
        if (!cabeza) {                          // Si la lista está vacía
            cout << "No hay procesos para eliminar.\n";
            return;
        }
        if (cabeza->id == id) {                 // Si el proceso a eliminar está al principio
            Proceso* aEliminar = cabeza;        // Guarda el nodo a eliminar
            cabeza = cabeza->siguiente;         // Actualiza la cabeza al siguiente
            delete aEliminar;                   // Libera la memoria del nodo
            cout << "Proceso eliminado.\n";
            return;
        }
        Proceso* actual = cabeza;               // Nodo actual para recorrer la lista
        // Busca el nodo anterior al que se va a eliminar
        while (actual->siguiente && actual->siguiente->id != id) {
            actual = actual->siguiente;
        }
        if (actual->siguiente) {                // Si se encontró el nodo a eliminar
            Proceso* aEliminar = actual->siguiente;
            actual->siguiente = aEliminar->siguiente;  // Enlaza el anterior con el siguiente del eliminado
            delete aEliminar;                   // Libera memoria
            cout << "Proceso eliminado.\n";
        } else {
            cout << "Proceso no encontrado.\n"; // No se encontró el proceso con el ID
        }
    }


        // Modifica la prioridad de un proceso dado su ID
    void modificarPrioridad(int id, int nuevaPrioridad) {
        Proceso* p = buscarPorID(id);           // Se busca el proceso con ese ID
        if (p) {
            p->prioridad = nuevaPrioridad;      // Si se encuentra, se actualiza su prioridad
            cout << "Prioridad modificada.\n";
        } else {
            cout << "Proceso no encontrado.\n"; // Si no se encuentra, se informa
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
