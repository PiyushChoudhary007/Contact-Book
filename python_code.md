import os
import pickle

class Contact:
    def __init__(self, name, phone, email):
        self.name = name
        self.phone = phone
        self.email = email
        self.next = None  # Self-referential link

class ContactBook:
    def __init__(self):
        self.head = None
        self.load_contacts()

    # Sorted insertion (alphabetical by name)
    def add_contact(self, name, phone, email):
        new_contact = Contact(name, phone, email)
        if not self.head or self.head.name.lower() > name.lower():
            new_contact.next = self.head
            self.head = new_contact
        else:
            current = self.head
            while current.next and current.next.name.lower() < name.lower():
                current = current.next
            new_contact.next = current.next
            current.next = new_contact
        self.save_contacts()
        print(f"Contact '{name}' added successfully.")

    def display_contacts(self):
        if not self.head:
            print("No contacts available.")
            return
        print("\nContact List:")
        current = self.head
        while current:
            print(f"Name: {current.name}, Phone: {current.phone}, Email: {current.email}")
            current = current.next

    def search_contact(self, name):
        current = self.head
        while current:
            if current.name.lower() == name.lower():
                print(f" Found Contact: {current.name}, {current.phone}, {current.email}")
                return current
            current = current.next
        print(f" Contact '{name}' not found.")
        return None

    def update_contact(self, name, new_phone, new_email):
        contact = self.search_contact(name)
        if contact:
            contact.phone = new_phone
            contact.email = new_email
            self.save_contacts()
            print(f"Contact '{name}' updated successfully.")

    def delete_contact(self, name):
        if not self.head:
            print("No contacts to delete.")
            return
        if self.head.name.lower() == name.lower():
            self.head = self.head.next
            self.save_contacts()
            print(f"Contact '{name}' deleted successfully.")
            return
        current = self.head
        while current.next and current.next.name.lower() != name.lower():
            current = current.next
        if current.next:
            current.next = current.next.next
            self.save_contacts()
            print(f"Contact '{name}' deleted successfully.")
        else:
            print(f"Contact '{name}' not found.")

    # File storage
    def save_contacts(self):
        with open("contacts.dat", "wb") as f:
            pickle.dump(self.head, f)

    def load_contacts(self):
        if os.path.exists("contacts.dat"):
            with open("contacts.dat", "rb") as f:
                self.head = pickle.load(f)

# -------- MENU BASED PROGRAM --------
if __name__ == "__main__":
    book = ContactBook()

    while True:
        print("\n====== Contact Book ======")
        print("1. Add Contact")
        print("2. Display Contacts")
        print("3. Search Contact")
        print("4. Update Contact")
        print("5. Delete Contact")
        print("6. Exit")

        choice = input("Choose option: ")

        if choice == "1":
            name = input("Enter name: ")
            phone = input("Enter phone: ")
            email = input("Enter email: ")
            book.add_contact(name, phone, email)

        elif choice == "2":
            book.display_contacts()

        elif choice == "3":
            name = input("Enter name to search: ")
            book.search_contact(name)

        elif choice == "4":
            name = input("Enter name to update: ")
            phone = input("Enter new phone: ")
            email = input("Enter new email: ")
            book.update_contact(name, phone, email)

        elif choice == "5":
            name = input("Enter name to delete: ")
            book.delete_contact(name)

        elif choice == "6":
            print("Exiting Contact Book... Goodbye!")
            break

        else:
            print("Invalid choice! Please try again.")
