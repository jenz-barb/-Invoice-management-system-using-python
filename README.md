import sqlite3

conn = sqlite3.connect('invoices.db')
c = conn.cursor()

# Create table if not exists
c.execute('''CREATE TABLE IF NOT EXISTS invoices
             (invoice_number TEXT PRIMARY KEY,
              customer_name TEXT,
              total_amount REAL,
              invoice_date TEXT)''')
conn.commit()

def create_invoice(invoice_number, customer_name, total_amount, invoice_date):
    try:
        c.execute('''INSERT INTO invoices VALUES (?, ?, ?, ?)''', (invoice_number, customer_name, total_amount, invoice_date))
        conn.commit()
        print("Invoice created successfully!")
    except sqlite3.IntegrityError:
        print("Error: Invoice number already exists.")

def read_invoice(invoice_number):
    c.execute('''SELECT * FROM invoices WHERE invoice_number = ?''', (invoice_number,))
    invoice = c.fetchone()
    if invoice:
        print("Invoice Number:", invoice[0])
        print("Customer Name:", invoice[1])
        print("Total Amount:", invoice[2])
        print("Invoice Date:", invoice[3])
    else:
        print("Invoice not found.")

def update_invoice(invoice_number, total_amount):
    c.execute('''UPDATE invoices SET total_amount = ? WHERE invoice_number = ?''', (total_amount, invoice_number))
    if c.rowcount == 0:
        print("Invoice not found.")
    else:
        conn.commit()
        print("Invoice updated successfully!")

def delete_invoice(invoice_number):
    c.execute('''DELETE FROM invoices WHERE invoice_number = ?''', (invoice_number,))
    if c.rowcount == 0:
        print("Invoice not found.")
    else:
        conn.commit()
        print("Invoice deleted successfully!")

def main():
    while True:
        print("\n1. Create Invoice")
        print("2. Read Invoice")
        print("3. Update Invoice")
        print("4. Delete Invoice")
        print("5. Exit")
        choice = input("Enter your choice: ")

        if choice == '1':
            invoice_number = input("Enter invoice number: ")
            customer_name = input("Enter customer name: ")
            total_amount = float(input("Enter total amount: "))
            invoice_date = input("Enter invoice date (YYYY-MM-DD): ")
            create_invoice(invoice_number, customer_name, total_amount, invoice_date)
        elif choice == '2':
            invoice_number = input("Enter invoice number: ")
            read_invoice(invoice_number)
        elif choice == '3':
            invoice_number = input("Enter invoice number: ")
            total_amount = float(input("Enter new total amount: "))
            update_invoice(invoice_number, total_amount)
        elif choice == '4':
            invoice_number = input("Enter invoice number: ")
            delete_invoice(invoice_number)
        elif choice == '5':
            break
        else:
            print("Invalid choice. Please try again.")

    conn.close()

if __name__ == "__main__":
    main()
