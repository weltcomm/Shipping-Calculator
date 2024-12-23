# Shipping-Calculator
import tkinter as tk
from tkinter import ttk, messagebox

def calculate_total_weight():
    """Calculates the total weight of the shipment based on selected products and their quantities."""
    try:
        total_weight = 0
        for product, quantity_var in quantities.items():
            quantity = int(quantity_var.get())
            unit_weight = products[product]
            total_weight += quantity * unit_weight
        weight_var.set(round(total_weight, 2))
    except ValueError:
        messagebox.showerror("Error", "Please enter valid quantities.")

def calculate_shipping():
    city = city_var.get()
    weight = float(weight_var.get())

    if city == "" or weight <= 0:
        messagebox.showerror("Error", "Please enter all the required information correctly.")
        return

    # Shipping rates
    rates = {
        "MAIN": {"ranges": [(1, 5), (6, 25), (26, 35)], "costs": [17000, 29541, 36656], "handling": 1000},
        "SECONDARY": {"ranges": [(1, 5)], "costs": [25800], "handling": 1000},
        "NATIONAL": {"ranges": [(1, 5)], "costs": [22000], "handling": 1000},
    }

    # Identify city type
    if city in main_cities:
        city_type = "MAIN"
    elif city in secondary_cities:
        city_type = "SECONDARY"
    elif city in national_cities:
        city_type = "NATIONAL"
    else:
        messagebox.showerror("Error", "City not found in the lists.")
        return

    city_data = rates[city_type]
    total_cost = 0

    # Determine shipping cost
    for (min_kg, max_kg), cost in zip(city_data["ranges"], city_data["costs"]):
        if min_kg <= weight <= max_kg:
            total_cost = cost
            break
    else:
        messagebox.showerror("Error", "Weight out of allowed range.")
        return

    # Add handling charge
    total_cost += city_data["handling"]

    # Display the result
    result_var.set(f"Total shipping cost: {total_cost:,} COP")

# Cities by category
main_cities = ["BOGOTA", "MANIZALES", "CALI", "CARTAGENA", "BARRANQUILLA", "TUNJA", "DOSQUEBRADAS", "ARMENIA", "SOACHA", "BUCARAMANGA", "PEREIRA", "RIONEGRO", "CUCUTA", "MONTERIA"]
secondary_cities = ["CUNDINAMARCA", "HUILA", "TIBANA", "MOCOA"]
national_cities = ["PASTO"]

# Products and weights (kg)
products = {
    "CINTA DELIVERY CANDADO": 0.3,
    "ETIQUETA DELIVERY 5X5": 0.45,
    "KIT EMPRENDEDOR": 0.24,
    "PRECINTO CORREA PLASTICA": 0.007,
    "CINTA DE EMBALAJE 4.8CM": 0.225,
    "CINTA DE EMBALAJE 7.2CM": 0.333,
    "ARSEL ROJO / AZUL": 0.46,
    "ARSEAL PERSONALIZADO TRANSFERENCIA": 0.46,
    "BOLSA DELIVER 36 X 30CM": 0.006,
    "BOLSA DELIVER 45 X 40CM": 0.01,
    "BOLSA KRAFT 25 X 37CM GENERIC": 0.0495,
    "BOLSA KRAFT 30 X 40CM GENERIC": 0.06,
    "BOLSA ECOMMERCE 19 X 25CM GENERIC": 0.0068,
    "BOLSA ECOMMERCE 25 X 33CM GENERIC": 0.012,
    "BOLSA ECOMMERCE 29 X 38CM GENERIC": 0.0142,
    "BOLSA ECOMMERCE 33 X 35CM GENERIC": 0.016,
    "BOLSA ECOMMERCE 41 X 45CM GENERIC": 0.0272,
    "BOLSA ECOMMERCE 48 X 60CM GENERIC": 0.035,
    "BOLSA ECOMMERCE 60 X 60CM GENERIC": 0.04,
    "PRECINTO TIPO SELLO BOTELLA": 0.06,
    "PRECINTO TIPO GUAYA METALICA": 0.04,
    "DISPENSADOR DE CINTA DELIVERY": 1.3,
}
# GUI
root = tk.Tk()
root.title("Shipping Calculator")

frame = ttk.Frame(root, padding="10")
frame.grid(row=0, column=0, sticky=(tk.W, tk.E, tk.N, tk.S))

# City
ttk.Label(frame, text="City:").grid(column=0, row=0, sticky=tk.W)
city_var = tk.StringVar()
city_entry = ttk.Combobox(frame, textvariable=city_var, values=main_cities + secondary_cities + national_cities)
city_entry.grid(column=1, row=0, sticky=(tk.W, tk.E))

# Products and quantities
products_frame = ttk.LabelFrame(frame, text="Products", padding="10")
products_frame.grid(column=0, row=1, columnspan=2, sticky=(tk.W, tk.E))

quantities = {}
row = 0
for product in products:
    ttk.Label(products_frame, text=product).grid(column=0, row=row, sticky=tk.W)
    quantity_var = tk.StringVar(value="0")
    quantities[product] = quantity_var
    ttk.Entry(products_frame, textvariable=quantity_var, width=5).grid(column=1, row=row, sticky=tk.E)
    row += 1

# Total weight
ttk.Label(frame, text="Total weight (kg):").grid(column=0, row=2, sticky=tk.W)
weight_var = tk.DoubleVar()
ttk.Entry(frame, textvariable=weight_var, state="readonly").grid(column=1, row=2, sticky=(tk.W, tk.E))

# Buttons
ttk.Button(frame, text="Calculate Weight", command=calculate_total_weight).grid(column=0, row=3)
ttk.Button(frame, text="Calculate Shipping", command=calculate_shipping).grid(column=1, row=3)

# Result
result_var = tk.StringVar()
ttk.Label(frame, textvariable=result_var, foreground="green").grid(column=0, row=4, columnspan=2)

# Column adjustments
for child in frame.winfo_children():
    child.grid_configure(padx=5, pady=5)

root.mainloop()
