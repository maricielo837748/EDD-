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
