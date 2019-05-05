# multithreading
Simple template &amp; example: Inheritance, Callback, Queue, Shutdown, Join, Deamon

```
# -*- coding: utf-8 -*-

import time
import threading
import queue

# https://eli.thegreenplace.net/2011/12/27/python-threads-communication-and-stopping

class AwesomeThread(threading.Thread):
    def __init__(self, callback, awesome_queue, *args, **kwargs):
        super(AwesomeThread, self).__init__(target=self.target_with_callback, *args, **kwargs)
        self.callback = callback
        self.awesome_queue = awesome_queue
        self.stoprequest = threading.Event()

    def join(self, timeout=None):
        # Waiting until queue is empty
        while not self.awesome_queue.empty(): time.sleep(0.2)

        self.stoprequest.set()
        print(self.name + ' joined')
        super(AwesomeThread, self).join(timeout)

    def target_with_callback(self):

        while not self.stoprequest.isSet():
            try:
                obj = self.awesome_queue.get(True, 0.05)

                # Do Something

                self.callback(obj)
                time.sleep(1)

            except queue.Empty:
                continue


awesome_queue = queue.Queue()


def Callback_function(obj):
    print ("Callback: Found '{0}' (id:{1}) in the queue".format(obj['obj'], obj['id']))

thread = AwesomeThread(
    name='AwesomeThread',
    awesome_queue=awesome_queue,
    callback=Callback_function
)

# If deamon=True OrderThread will be killed when main thread ends
# (if thread.join() isn't called).
# Remaining orders in the queue are not processed.
# thread.setDaemon(True)

thread.start()

awesome_queue.put({'id': 1, 'obj': 'Bed'})
awesome_queue.put({'id': 2, 'obj': 'Fridge'})
awesome_queue.put({'id': 3, 'obj': 'Desk'})

print('Put 3 things in the queue')

time.sleep(1)

awesome_queue.put({'id': 4, 'obj': 'Stove'})

print('Put another item in the queue')

# Blocks until queue is empty
thread.join()

print('Main thread ends')
```
