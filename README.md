Instrukcje do obslugi konsoli
# Dodaj autora
author1 = Author(name="John Doe")
author2 = Author(name="Jane Smith")
author3 = Author(name="Michael Johnson")
author4 = Author(name="Emily Brown")

db.session.add(author1)
db.session.add(author2)
db.session.add(author3)
db.session.add(author4)
db.session.commit()

# Dodaj książkę
book1 = Book(title="Sample Book 1")
book2 = Book(title="Sample Book 2")
book3 = Book(title="Sample Book 3")
book4 = Book(title="Sample Book 4")

book1.authors.append(author1)
book2.authors.append(author2)
book3.authors.append(author3)
book4.authors.append(author4)

db.session.add(book1)
db.session.add(book2)
db.session.add(book3)
db.session.add(book4)

db.session.commit()
# Po wypożyczeniu książki
from datetime import datetime, timedelta
from sqlalchemy import func
imie = 'Alice'  # Podac imie osoby ktora oddaje ksiazke
tytul = 'Sample Book 1'  # Podac tytul ksiazki

book1 = Book.query.filter(func.lower(Book.title) == func.lower(tytul)).first()
if book1:
    borrowing = Borrowing(borrower_name= imie, return_date=datetime.now() + timedelta(days=14), book=book1)
    db.session.add(borrowing)
    book1.is_available = False  # Zaktualizuj dostępność książki na False
    db.session.commit()
else:
    print("Nie znaleziono książki o podanej nazwie")
# Po zwróceniu książki
imie = 'Alice'  # Podac imie osoby ktora oddaje ksiazke
returned_borrowing = None
for borrowing in book1.borrowings:
    if borrowing.borrower_name == imie:
        returned_borrowing = borrowing
        break
if returned_borrowing:
    db.session.delete(returned_borrowing)
    book1.is_available = True  # Zaktualizuj dostępność książki na True
    db.session.commit()
