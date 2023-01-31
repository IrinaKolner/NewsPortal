D5.9 (сюда дублирую на всякий случай)
+ пара вопросов -> 
1) появились сомнения насчет автора новости. У новости обязательно должен быть автор? Если нет, то надо изначально как-то это указать (например, что по дефолту автор - None, или что-то типа того)?
2) мне все время кажется, что чего-то не хватает. Я все, что нужно сделала?

Python manage.py shell

from django.contrib.auth.models import User
from news.models import *
from django.db.models import Sum



Создать двух пользователей (с помощью метода User.objects.create_user('username')). 
u1 = User.objects.create_user('User1')
u2 = User.objects.create_user('User2')



Создать два объекта модели Author, связанные с пользователями.
au1 = Author.objects.create(user=u1)
au2 = Author.objects.create(user=u2)



Добавить 4 категории в модель Category.
cat1 = Category.objects.create(news_category='CU')
cat2 = Category.objects.create(news_category='SC')
cat3 = Category.objects.create(news_category='TE')
cat4 = Category.objects.create(news_category='PO')



Добавить 2 статьи и 1 новость.
Шаблон:
a1 = Post.objects.create(author=au1, post_type='AR', title='<название>', text='<текст>')
a2 = Post.objects.create(author=au2, post_type='AR', title='<название статьи>', text='<текст>')
n1 = Post.objects.create(author=au1, post_type='NE', title='<название новости>', text='<текст новости>')

Полный вариант:
a1 = Post.objects.create(author=au1, post_type='AR', title='Ученые хотят найти место битвы Тимура и Тохтамыша под Самарой', text='Археологи России и Узбекистана намерены в течение пяти лет установить точное место в Самарской области, где проходила битва, в ходе которой среднеазиатский правитель, полководец Тимур (Тамерлан) одержал легендарную победу над ханом Золотой Орды Тохтамышем')
a2 = Post.objects.create(author=au2, post_type='AR', title='Чем грозят изменения в ядре Земли', text='Вращение твердого внутреннего ядра Земли недавно замедлилось практически полностью и может измениться на противоположное. К такому выводу пришли ученые Пекинского университета. С чем это связано и какие последствия ждут планету — в материале РИА Новости.')
n1 = Post.objects.create(author=au1, post_type='NE', title='Мариинка в День снятия блокады покажет балеты на музыку Шостаковича', text=' В Мариинке пройдет несколько событий ко Дню полного снятия блокады Ленинграда, сообщили в пресс-службе театра.')


появились сомнения насчет автора новости. У новости обязательно должен быть автор? Если нет, то надо изначально как-то это указать (например, что по дефолту автор - None, или что-то типа того)?



Присвоить им категории (как минимум в одной статье/новости должно быть не меньше 2 категорий).
a1.categories.add(cat1, cat2)
a2.categories.add(cat2), n1.categories.add(cat1)



Создать как минимум 4 комментария к разным объектам модели Post (в каждом объекте должен быть как минимум один комментарий).
Шаблон:
c1 = Comment.objects.create(post=a1, user=u1, comment_text='<text>')
c2 = Comment.objects.create(post=a2, user=u2, comment_text='<text>')
c3 = Comment.objects.create(post=n1, user=u1, comment_text='<text>')
c4 = Comment.objects.create(post=n1, user=u2, comment_text='<text>')

Полный вариант:
c1 = Comment.objects.create(post=a1, user=u1, comment_text='Однажды, в студеную зимнюю пору Я из лесу вышел; был сильный мороз. Гляжу, поднимается медленно в гору Лошадка, везущая хворосту воз.')
c2 = Comment.objects.create(post=a2, user=u2, comment_text='И шествуя важно, в спокойствии чинном, Лошадку ведет под уздцы мужичок В больших сапогах, в полушубке овчинном, В больших рукавицах... а сам с ноготок!')
c3 = Comment.objects.create(post=n1, user=u1, comment_text='На эту картину так солнце светило, Ребенок был так уморительно мал, Как будто всё это картонное было, Как будто бы в детский театр я попал!')
c4 = Comment.objects.create(post=n1, user=u2, comment_text='Но мальчик был мальчик живой, настоящий, И дровни, и хворост, и пегонький конь, И снег, до окошек деревни лежащий, И зимнего солнца холодный огонь -')



Применяя функции like() и dislike() к статьям/новостям и комментариям, скорректировать рейтинги этих объектов.
Обновить рейтинги пользователей.
Post.like(a1)
Post.like(a2)
Post.like(n1)
Comment.like(c1)
Comment.like(c2)
c3  -> Comment.like(c3)
c4  -> Comment.like(c4)



Вывести username и рейтинг лучшего пользователя (применяя сортировку и возвращая поля первого объекта).
Вывести дату добавления, username автора, рейтинг, заголовок и превью лучшей статьи, основываясь на лайках/дислайках к этой статье.
best = Author.objects.order_by('-user_rating').values('user')[0]['user']
User.objects.get(id = best)



Вывести все комментарии (дата, пользователь, рейтинг, текст) к этой статье.
ar_comm = Post.objects.values('pk').order_by('-post_rating')[0]['pk']
Comment.objects.values('comment_text').filter(post_id = ar_comm)
