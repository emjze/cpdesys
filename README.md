from opcua import Client
import logging

logging.basicConfig(level=logging.INFO)

PLC_ENDPOINT = "opc.tcp://127.0.0.1:4840"

try:
    print("[+] Intentando conectar al PLC...")
    client = Client(PLC_ENDPOINT)  # DEFINE EL CLIENTE AQUÍ
    client.connect()
    print("[+] Conexión exitosa al PLC")

    # Obtener nodo raíz
    root = client.get_root_node()
    print("[+] Nodo raíz:", root)

    # Obtener nodo "Objects" que normalmente contiene las variables
    objects_node = client.get_objects_node()
    print("\n[+] Explorando el nodo 'Objects':")

    # Recorrer todos los hijos del nodo "Objects"
    for node in objects_node.get_children():
        print("\n[+] Nodo:", node)
        print("[+] BrowseName:", node.get_browse_name())
        print("[+] DisplayName:", node.get_display_name())
        print("[+] NodeId:", node.nodeid)

        # Verificar si el nodo tiene variables
        for var in node.get_children():
            print("  - Variable:", var)
            print("    - BrowseName:", var.get_browse_name())
            print("    - DisplayName:", var.get_display_name())
            print("    - NodeId:", var.nodeid)
            try:
                print("    - Valor actual:", var.get_value())
            except Exception as e:
                print("    - No se pudo leer el valor:", e)

except Exception as e:
    print("[-] Error al explorar nodos:", e)

finally:
    # Solo desconectar si el cliente fue creado y está conectado
    try:
        client.disconnect()
        print("[+] Conexión cerrada correctamente")
    except Exception as e:
        print("[-] Error al cerrar la conexión:", e)
