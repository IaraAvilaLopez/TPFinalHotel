# TPFinalHotel
from datetime import datetime, time

class Habitacion:
    def __init__(self, piso, numero, tipo):
        self.piso = piso #Piso del hotel
        self.numero = numero #Numero de habitacion en este piso
        self.tipo = tipo #"doble" o "triple"
        self.estado = "libre" #'libre, 'reservada', 'ocupada'
        self.huespedes = [] #Lista de huespedes actuales
        self.fecha_ingreso = None #fecha y hora de ingreso
    
    def capacidad_maxima(self):
        if self.tipo == "doble":
            return 2
        elif self.tipo == "triple":
            return 3
    
    def agregar_huesped(self, huesped):
        if self.estado == "ocupada":
            print("No se pueden agregar huéspedes: la habitación ya esta ocupada.")
            return
        
        if len(self.huespedes) < self.capacidad_maxima():
            self.huespedes.append(huesped)

        else:
            print("No se puede agregar más huéspedes: capacidad máxima alcanzada.")
    
    def check_in(self):
        if self.estado == "reservada" or self.estado == "libre":
            self.estado = "ocupada"
            self.fecha_ingreso = datetime.now()
            print(f"Habitación {self.numero} ocupada desde {self.fecha_ingreso}")
        else:
            print("No se puede hacer check-in: habitación ya ocupada.")
    
    def check_out(self):
        if self.estado != "ocupada":
            print("No se puede hacer check-out: la habitación no esta ocupada.")
            return
        if not self.fecha_ingreso:
            print("No hay fecha de ingreso registrada.")
            return
        ahora = datetime.now()
        diferencia = ahora - self.fecha_ingreso
        noches = diferencia.days
        
       # Hora límite de salida
        hora_salida_tope = time(10, 0)
    
        if noches == 0:
            if ahora.time() > hora_salida_tope:
                print("Se superó la hora de salida: se cobrará media noche.")
                noches = 0.5
            else:
                print("Estadía menor a un día y dentro del horario permitido. No se cobra.")
                noches = 0
        else:
            if ahora.time() > hora_salida_tope:
                print("Se superó la hora de salida: se cobrará media noche adicional.")
                noches += 0.5
        
        #Precio base por noche de habitacion doble
        precio_base = 100
        cantidad = len(self.huespedes)
        if cantidad == 1:
            precio_noche = precio_base * 0.8
        elif cantidad == 2:
            precio_noche = precio_base
        elif cantidad == 3:
            precio_noche = precio_base * 1.3
        else:
            print("Cantidad de huespedes inválida.")
            return
        total = noches * precio_noche
        print(f"Total a pagar por {noches} noches: ${total:.2f}")

        #Liberar habitación
        self.estado = "libre"
        self.huespedes.clear()
        self.fecha_ingreso = None

class Huesped:
    def __init__(self, nombre, apellido, dni, correo):
        self.nombre = nombre #Nombre del huesped
        self.apellido = apellido #Apellido del huesped
        self.dni = dni #DNI del Huesped
        self.correo = correo #Correo Electronico

    def mostrar_datos(self):
        print(f"{self.nombre} {self.apellido} - DNI: {self.dni} - Correo: {self.correo}")

def menu_habitacion(hab):
    while True:
        print("\n--- Menu de habitación ---")
        print("1. Agregar huésped")
        print("2. Hacer check-in")
        print("3. Hacer check-out")
        print("4. Mostrar estado")
        print("5. Salir")

        opcion = input("Seleccione una opción: ")
        if opcion == "1":
            if len(hab.huespedes) >= hab.capacidad_maxima():
                print("Ya no se pueden agregar mas huéspedes. ")
                continue
            
            nombre = input("Nombre del huésped: ")
            apellido = input("Apellido del huésped: ")
            dni = input("DNI del huésped: ")
            correo = input("correo del huésped: ")
            huesped = Huesped(nombre, apellido, dni, correo)
            hab.agregar_huesped(huesped)
        
        elif opcion == "2":
            hab.check_in()
        
        elif opcion == "3":
            hab.check_out()

        elif opcion == "4":
            print(f"\nHabitación {hab.numero} - Piso {hab.piso} - Tipo {hab.tipo}")
            print(f"Estado: {hab.estado}")
            print("Huéspedes:")
            if not hab.huespedes:
                print("No hay huéspedes registrados.")
            for h in hab.huespedes:
                h.mostrar_datos()

        elif opcion == "5":
            print("Saliendo...")
            break
        else:
            print("Opción no válida.") 

piso = int(input("Ingrese el número de piso: "))
numero = int(input("Ingrese el número de habitación: "))
tipo = input("Ingrese el tipo de habitación ('doble' o 'triple'): ")

habitacion = Habitacion(piso, numero, tipo)
menu_habitacion(habitacion)
