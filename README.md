Evaluación parcial -- Resuelta
Asignatura: Programación en C

Respuestas desarrolladas con base en las preguntas del documento
original.
1. ¿Qué es una función en programación y cómo se define una función en C?
Una función es un bloque de instrucciones que realiza una tarea
específica y puede reutilizarse desde distintas partes del programa. En
C se define indicando el tipo de dato que devuelve, el nombre, los
parámetros y el cuerpo.
int sumar(int a, int b) {
    return a + b;
}
2. ¿Qué ocurre cuando se invoca una función?
El programa transfiere el control a esa función. Se pasan los argumentos
a sus parámetros, se ejecutan sus instrucciones y, cuando termina (por
return o al llegar al final si es void), el control vuelve al punto
desde el que fue llamada. Si la función devuelve un valor, este puede
utilizarse en la expresión que hizo la llamada.
3. Declaración de un arreglo bidimensional para las ventas
Se necesitan 3 filas (sucursales) y 5 columnas (días de lunes a
viernes):
float ventas[3][5];
Por ejemplo, ventas[0][0] representa las ventas del lunes de la
primera sucursal.
4. Programa en C que convierte las vocales de una cadena a mayúsculas
#include <stdio.h>

int main(void) {
    char cadena[200];
    int i;

    printf("Escribe una cadena: ");
    if (fgets(cadena, sizeof(cadena), stdin) == NULL)
        return 1;

    for (i = 0; cadena[i] != '\0'; i++) {
        switch (cadena[i]) {
            case 'a': cadena[i] = 'A'; break;
            case 'e': cadena[i] = 'E'; break;
            case 'i': cadena[i] = 'I'; break;
            case 'o': cadena[i] = 'O'; break;
            case 'u': cadena[i] = 'U'; break;
        }
    }

    printf("Resultado: %s", cadena);
    return 0;
}
El programa lee una línea completa con fgets y cambia únicamente las
vocales minúsculas; las demás letras y caracteres permanecen igual.
5. ¿Qué es un apuntador y cómo se declara en C?
Un apuntador (puntero) es una variable que almacena la dirección de
memoria de otra variable. Se declara con el operador * junto al tipo
al que apunta.
int *p;
Para guardar la dirección de una variable se usa &:
int n = 5;
p = &n;
Para acceder al valor apuntado se usa *p.
6. Diferencia entre printf("x=%d", x) y printf("x=%d", *x)
Con el código:
int *x, y = 7;
x = &y;
x contiene la dirección de memoria de y. Por tanto, imprimir x
intenta mostrar una dirección (para hacerlo correctamente se usa %p y
se convierte a (void *)x).

En cambio, *x desreferencia el apuntador y obtiene el valor almacenado
en esa dirección: 7. Para este caso se usa %d.
7. Estado de las variables y apuntadores
El código establece i en la dirección 100, ptrToi en 104 y
ptrToPtrToi en 108. Los valores no inicializados se indican como
indefinido.

  Momento                  i (100)   ptrToi (104)   ptrToPtrToi (108)
  Antes de línea 7        Indefinido       Indefinido            Indefinido
Después de línea 8      Indefinido       Indefinido                   104
Después de línea 10     Indefinido              100                   104
Antes de línea 11       Indefinido              100                   104
Después de línea 12             20              100                   104
Después de línea 13             30              100                   104

Explicación:
- En la línea 8, ptrToPtrToi = &ptrToi, así que guarda la dirección
  104.
- En la línea 9, ptrToi = &i, así que guarda la dirección 100.
- La línea 11 asigna 10 a i.
- La línea 12, *ptrToi = 20, modifica i a 20.
- La línea 13, **ptrToPtrToi = 30, vuelve a modificar i, ahora a
  30.
Los dos apuntadores conservan sus direcciones.
8. Diferencia entre malloc y calloc
- malloc(n) reserva n bytes de memoria dinámica, pero no
  inicializa su contenido; sus valores quedan indeterminados.
- calloc(cantidad, tamaño) reserva espacio para cantidad
  elementos de tamaño bytes cada uno y deja todos los bytes
  inicializados en cero.
Ambas devuelven un apuntador (o NULL si falla la reserva) y la memoria
debe liberarse con free().
9. Estructura cord3D y arreglo dinámico de puntos
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    float x, y, z;
} cord3D;

int main(void) {
    int n, i;
    cord3D *puntos;

    printf("Cantidad de puntos: ");
    if (scanf("%d", &n) != 1 || n <= 0) {
        printf("Cantidad no valida.\n");
        return 1;
    }

    puntos = malloc((size_t)n * sizeof(cord3D));
    if (puntos == NULL) {
        printf("No se pudo reservar memoria.\n");
        return 1;
    }

    for (i = 0; i < n; i++) {
        printf("Punto %d (x y z): ", i + 1);
        if (scanf("%f %f %f",
                  &puntos[i].x, &puntos[i].y, &puntos[i].z) != 3) {
            printf("Entrada no valida.\n");
            free(puntos);
            return 1;
        }
    }

    printf("\nPuntos capturados:\n");
    for (i = 0; i < n; i++)
        printf("(%.2f, %.2f, %.2f)\n",
               puntos[i].x, puntos[i].y, puntos[i].z);

    free(puntos);
    return 0;
}
10. Función para guardar los puntos en archivo de texto o binario
La siguiente función reutiliza la estructura cord3D definida en el
ejercicio 9.
#include <stdio.h>

int guardarPuntos(const char *nombre, const cord3D *puntos,
                  int n, int binario) {
    FILE *archivo;
    int i;

    archivo = fopen(nombre, binario ? "wb" : "w");
    if (archivo == NULL)
        return 0;

    if (binario) {
        if (fwrite(puntos, sizeof(cord3D), n, archivo) != (size_t)n) {
            fclose(archivo);
            return 0;
        }
    } else {
        for (i = 0; i < n; i++) {
            if (fprintf(archivo, "%.2f %.2f %.2f\n",
                        puntos[i].x, puntos[i].y, puntos[i].z) < 0) {
                fclose(archivo);
                return 0;
            }
        }
    }

    fclose(archivo);
    return 1;
}
El parámetro binario controla el formato:
- 0: guarda texto legible con fprintf.
- 1: guarda los datos en formato binario con fwrite.
La función devuelve 1 si la escritura termina correctamente y 0 si
ocurre un error. Esta función se puede colocar después de la definición
de cord3D y llamarse antes de liberar el arreglo dinámico.
