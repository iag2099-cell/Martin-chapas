<img width="636" height="382" alt="Captura de pantalla 2025-12-10 a las 19 17 00" src="https://github.com/user-attachments/assets/6b109233-1e16-4241-a52c-e0cbcead71bf" />
# Calculadora Python (1-100, suma y resta)

Este repositorio contiene un programa en Python que calcula expresiones que contienen solo números enteros del 1 al 100 y operadores `+` y `-`.  
También incluye una versión interactiva para probar las expresiones.

---

## Código Python

```python
import re

def compute(expr: str):
    """
    Calcula expresiones que contienen solo enteros (1-100) y operadores + o -.
    Devuelve (resultado, None) en caso de éxito o (None, error_message) en caso de error.
    """
    if not isinstance(expr, str):
        return None, "La expresión debe ser texto."
    expr = expr.strip()
    if expr.lower() in ('q', 'exit', 'salir'):
        return None, "exit"
    if expr == "":
        return None, "Expresión vacía."
    # Solo permitir dígitos, espacios y signos + y -
    if re.search(r"[^0-9+\- \t]", expr):
        return None, "Caracteres no permitidos. Usa solo números y + o -."
    # Tokenizar números y operadores
    tokens = re.findall(r"\d+|[+\-]", expr)
    if not tokens:
        return None, "No se han encontrado tokens válidos."
    # Expresión debe empezar con número
    if not re.match(r"^\s*\d", expr):
        return None, "La expresión debe comenzar por un número (ej: 2+3)."
    # Convertir y validar números
    nums = []
    ops = []
    expect_num = True
    for t in tokens:
        if expect_num:
            if re.fullmatch(r"\d+", t):
                n = int(t)
                if n < 1 or n > 100:
                    return None, f"Número fuera de rango (1-100): {n}."
                nums.append(n)
                expect_num = False
            else:
                return None, f"Se esperaba un número y vino '{t}'."
        else:
            if t in ('+', '-'):
                ops.append(t)
                expect_num = True
            else:
                return None, f"Se esperaba '+' o '-' y vino '{t}'."
    if expect_num:
        return None, "La expresión termina con un operador. Debe terminar con un número."
    # Evaluación izquierda a derecha
    result = nums[0]
    for i, op in enumerate(ops):
        if op == '+':
            result += nums[i+1]
        else:
            result -= nums[i+1]
    return result, None

# Programa interactivo
def repl():
    print("Calculadora (solo sumas y restas, números 1..100). Escribe 'q' o 'exit' para salir.")
    while True:
        try:
            s = input(">> ").strip()
        except EOFError:
            print("\nSaliendo.")
            break
        if s.lower() in ('q', 'exit', 'salir'):
            print("Adiós.")
            break
        res, err = compute(s)
        if err is None:
            print(res)
        elif err == "exit":
            print("Adiós.")
            break
        else:
            print("Error:", err)

if __name__ == "__main__":
    # Pruebas demostrativas al arrancar el archivo
    tests = ["2+3", " 10 -7 ", "5+10-3", "100-1", "2*3", "0+5", "101-1", "+3-2", "3-"]
    print("Pruebas automáticas rápidas:")
    for t in tests:
        res, err = compute(t)
        if err is None:
            print(f"  {t} -> {res}")
        else:
            print(f"  {t} -> ERROR: {err}")
    print("\nAhora puedes usar la calculadora interactiva:")
    repl()
