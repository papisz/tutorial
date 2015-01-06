# ORM i obiekty QuerySet w Django

Z tego rozdziału dowiesz się, w jaki sposób Django łączy się z bazą danych i gromadzi w niej informacje. Do dzieła!

## Czym jest QuerySet?

Najprościej mówiąc, QuerySet jest listą obiektów danego modelu. Za jego pomocą możesz wczytywać informacje z bazy danych, jak również je filtrować i układać w określonej kolejności.

Najłatwiej uczyć się na przykładach. To co, spróbujemy?

## Konsola Django

Otwórz konsolę i wpisz następujące polecenie:

    (myvenv) ~/djangogirls$ python manage.py shell
    

Efekt powinien wyglądać tak:

    (InteractiveConsole)
    >>>
    

Jesteś teraz w interaktywnej konsoli Django. Przypomina ona nieco wiersz poleceń Pythona, z odrobiną dodatkowej magii Django :) Oczywiście możesz także używać w niej wszystkich poleceń Pythona.

### Wszystkie obiekty

Na początek spróbujmy wyświetlić wszystkie nasze wpisy. Zrób to następującym poleceniem:

    >>> Post.objects.all()
    Traceback (most recent call last):
          File "<console>", line 1, in <module>
    NameError: name 'Post' is not defined
    

Ups! Wyskoczył błąd. Mówi on nam, że nie istnieje coś takiego jak 'Post'. Oczywiście -- zapomniałyśmy go wcześniej zaimportować!

    >>> from blog.models import Post
    

Nic skomplikowanego: importujemy model `Post` z `blog.models`. Spróbujmy jeszcze raz wyświetlić wszystkie wpisy:

    >>> Post.objects.all()
    []
    

Pojawiła się pusta lista. To chyba dobrze, prawda? Przecież nie dodałyśmy jeszcze żadnego wpisu! Naprawmy to.

### Tworzenie obiektu

Obiekt Post w bazie danych tworzymy tak:

    >>> Post.objects.create(author=user, title='Sample title', text='Test')
    

Ale brakuje nam jednego składnika: zmiennej `user`. Musimy przekazać instancję modelu `User` jako autora. Jak to zrobić?

Najpierw zaimportujmy model User:

    >>> from django.contrib.auth.models import User
    

Jakich użytkowników mamy w bazie danych? Spróbuj tak:

    >>> User.objects.all()
    []
    

Żadnych użytkowników! No to stwórzmy jednego:

    >>> User.objects.create(username='ola')
    <User: ola>
    

Jakich użytkowników mamy teraz w bazie danych? Spróbuj tak:

    >>> User.objects.all()
    [<User: ola>]
    

Super! Teraz uzyskajmy dostęp do naszej instancji użytkownika:

    user = User.objects.get(username='ola')
    

Jak widzisz, za pomocą polecenia `get` pobrałyśmy z bazy obiekt użytkownika (`User`) z właściwością `username` o wartości 'ola'. Elegancko! Oczywiście musisz wprowadzić tam swoją nazwę użytkownika.

Teraz możemy wreszcie stworzyć nasz pierwszy wpis:

    >>> Post.objects.create(author = user, title = 'Sample title', text = 'Test')
    

Hura! Chciałabyś sprawdzić, czy się udało?

    >>> Post.objects.all()
    [<Post: Sample title>]
    

### Dodajemy więcej wpisów

Możesz teraz się pobawić i utworzyć więcej wpisów, żeby zobaczyć, jak to działa. Dodaj jeszcze 2-3 i jedziemy do następnej cześci.

### Filtrowanie obiektów

Niezmiernie istotną cechą QuerySetów jest możliwość ich filtrowania. Dajmy na to, że chciałybyśmy znaleźć wszystkie wpisy dodane przez użytkowniczkę (User) o nazwie ola. Skorzystamy z metody `filter` zamiast `all` w `Post.objects.all()`. W nawiasach wpiszemy jeden lub więcej warunków, które muszą zostać spełnione, żeby nasz wpis znalazł się w QuerySecie. W naszej sytuacji chodzi o to, aby `author` odpowiadał zmiennej `user`. W Django zapisujemy to tak: `author=user`. Teraz nasz kawałek kodu wygląda mniej-więcej tak:

    >>> Post.objects.filter(author=user)
    [<Post: Sample title>, <Post: Post number 2>, <Post: My 3rd post!>, <Post: 4th title of post>]
    

A gdybyśmy chciały wyświetlić wszystkie wpisy zawierające słowo 'title' w polu `title`?

    >>> Post.objects.filter(title__contains = 'title')
    [<Post: Sample title>, <Post: 4th title of post>]
    

> **Uwaga:** Pomiędzy `title` a `contains` znajdują się dwa znaki podkreślenia (`_`). ORM w Django używa takiej składni, aby oddzielić nazwy pól ("title") od operacji lub filtrów ("contains"). Jeśli użyjesz tylko jednego, zobaczysz błąd o treści "FieldError: Cannot resolve keyword title_contains".

Możemy także wyświetlić listę wszystkich opublikowanych wpisów. W tym celu odfiltrujmy wszystkie wpisy, które mają ustawioną datę publikacji (`published_date`):

    >>> Post.objects.filter(published_date__isnull=False)
    []
    

Niestety, żaden z naszych wpisów nie został jeszcze opublikowany. Ale da się to zmienić! Zacznij od pobrania wpisu, który chcesz opublikować:

    >>> post = Post.objects.get(id = 1)
    

A następnie opublikuj go za pomocą metody `publish`!

    >>> post.publish()
    

Teraz spróbujmy jeszcze raz wyświetlić listę opublikowanych wpisów (wciśnij trzykrotnie klawisz ze strzałką do góry, a następnie zatwierdź klawiszem Enter):

    >>> Post.objects.filter(published_date__isnull=False)
    [<Post: Sample title>]
    

### Kolejność obiektów

QuerySety umożliwiają również porządkowanie list obiektów według określonej kolejności. Spróbujmy uporządkować je według daty utworzenia, czyli zawartości pola `created_date`:

    >>> Post.objects.all().order_by('created_date')
    [<Post: Sample title>, <Post: Post number 2>, <Post: My 3rd post!>, <Post: 4th title of post>]
    

Możemy także odwrócić kolejność poprzez dodanie `-` na początku:

    >>> Post.objects.all().order_by('-created_date')
    [<Post: 4th title of post>,  <Post: My 3rd post!>, <Post: Post number 2>, <Post: Sample title>]
    

Doskonale! Jesteś teraz gotowa na następną część! Zamknij konsolę poleceniem:

    >>> exit()
    $