 ```
 # signals.py

from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import User

@receiver(post_save, sender=User)
def user_created_handler(sender, instance, created, **kwargs):
    if created:
        print(f"User {instance.username} has been created.") 

# models.py

from django.db import models

class MyModel(models.Model):
    name = models.CharField(max_length=100)

# views.py

def my_view(request):
    # This will trigger the post_save signal synchronously
    MyModel.objects.create(name='Test Object')
```


# Q.1 By default are django signals executed scyncrounusly or ascyncrounusly?

        By default, Django signals are executed synchronously. When a signal is sent, the connected receivers (functions)
        are executed immediately and one after the other. This means the code that triggered the signal will wait for all 
        the receivers to complete before continuing execution.
        in the above example if a new user object is created then the user-created-handler is executed synchronously
        which means the post_save method(signal) should finish before the user-created-handler is executed





# Q.2   Do django signals run in the same thread as the caller?

        By default, Django signals run in the same thread as the caller. When a signal is triggered, the registered receivers are executed synchronously, meaning they are run in the same thread and blocking the execution of the caller until the signal processing is complete.
        In the above example, the post_save signal for MyModel is triggered after a new object is created. The signal handler my_handler runs synchronously, meaning "This will print after the signal handler completes" only prints after the signal logic is executed.


# Q.3   By default do django signals run in the same database transaction as the caller?

        Yes, by default Django signals run in the same database transaction as the caller. This means that if a signal is triggered during a database operation (like saving a model), the signal's receiver function is executed as part of the same transaction. If the transaction is rolled back, the effects of the signal (if any) are also rolled back.
        In the  above example, the post_save signal runs within the same transaction. If an error occurs within the transaction.atomic() block, the signal effects will also be rolled back.