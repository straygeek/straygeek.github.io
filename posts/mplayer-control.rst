.. link: 
.. description: 
.. tags: لینوکس,mplayer
.. date: 2013/08/20 21:51:31
.. title: کنترل MPlayer به وسیله کلیدهای لپ‌تاپ
.. slug: mplayer-control

مدتی پیش، میخواستم راهی برای کنترل MPlayer از گوشیم پیدا بکنم. نتونستم با `MPDها <http://en.wikipedia.org/wiki/Music_Player_Daemon>`_ کنار بیام و بالاخره، آستین‌ها رو بالا زدم تا خودم کاری بکنم! ولی فعلا درمورد اون نمینویسم. در این بین فهمیدم چطور میشه ام‌پلیر رو از دور (منظور جایی غیر از خود ام‌پلیر) کنترل کرد و به فکرم افتاد از شرتکات‌های کنترل پلیر لپ‌تاپم هم استفاده‌ای بکنم. برای VLC و بقیه پلیرها احتمالا چیز آنچنانی نیست و اگه با MPDها راحت بودم، واسه ام‌پلیر هم میتونستم خیلی آسون‌تر کاری بکنم. ولی فعلا اون‌ها رو میذارم کنار.

ام‌پلیر میتونه به وسیله `فایل‌های پایپ <http://en.wikipedia.org/wiki/Named_pipe>`_ کنترل بشه. من برای فایل‌های پایپم، یه دایرکتوری مخصوص به آدرس ‎/media/fifo دارم. اونجا یه فایل فیفو به اسم mplfifo درست میکنم:

.. code:: bash
	  
   mkfifo /media/fifo/mplfifo

الان باید شرتکات‌ها رو روی اوپن‌باکس تنظیم بکنم. احتمالا میدونین که هر کلیدی روی صفحه کلیدتون یه کدی داره. مثلا k همون k هست. ولی بعضی وقت‌ها اینقدر ساده نیست. مثلا کد کپس‌لاک من `ISO_Next_Group` هست! کلیدهای کنترل پلیر هم از این نوع هستن.

برای پیدا کردن این کدها، یه راه ساده‌ای وجود داره. میتونین از برنامه xev که داخل خود X هست استفاده بکنین. اگه میخواین مستقیم به کد کلید برسین، میتونین از این تکه کد کوچیک استفاده بکنین:

.. code:: bash
	  
   xev | grep -oP "keycode \S* \(keysym \S*?, \S*?\)" | cut -d, -f2 | tr -d \) | tr -d " "

کلیدهایی که میخواین رو بزنین و بعد پنجره رو ببندین (کل کد رو با Ctrl+C قطع نکنین!). کدهای کلیدهای کنترل پلیر من اینا هستن:

.. code::
   
   XF86AudioPlay
   XF86AudioStop
   XF86AudioNext
   XF86AudioPrev

حالا شرتکات‌ها رو میسازیم. باید دستور رو به فایل پایپ بفرستیم. مثلا برای توقف و اجرای پلیر، از این خط میشه استفاده کرد:

.. code:: bash
	  
   echo pause > /media/fifo/mplfifo

اگه برای احرای شرتکات‌ها از bash (یا شل دیگه‌ای که از «<» پشتیبانی بکنه) استفاده نشه، مجبوریم خودمون بدیمش به بش:

.. code:: bash
	  
   bash -c 'echo pause > /media/fifo/mplfifo'

دستوراتی که من استفاده میکنم، `pause`،‏ `stop`،‏ `pt_step 1` و `pt_step -1` هستن. لیست کامل دستورات رو میتونین از `اینجا <http://www.mplayerhq.hu/DOCS/tech/slave.txt>`_ ببینین. برای مثال، شرتکات‌های من توی اوپن‌باکس به این صورت هستن:

.. code:: xml
	  
    <keybind key="XF86AudioPlay">
      <action name="Execute">
        <execute>bash -c 'echo pause > /media/fifo/mplfifo'</execute>
      </action>
    </keybind>
    <keybind key="XF86AudioStop">
      <action name="Execute">
        <execute>bash -c 'echo stop > /media/fifo/mplfifo'</execute>
      </action>
    </keybind>
    <keybind key="XF86AudioNext">
      <action name="Execute">
        <execute>bash -c 'echo pt_step 1 > /media/fifo/mplfifo'</execute>
      </action>
    </keybind>
    <keybind key="XF86AudioPrev">
      <action name="Execute">
        <execute>bash -c 'echo pt_step -1 > /media/fifo/mplfifo'</execute>
      </action>
    </keybind>

خوب، حالا نوبت خود ام‌پلیر هست. من همیشه نمیخوام از این شرتکات‌ها استفاده بکنم. مثلا موقع دیدن فیلم لازمم نمیشه و اگه آهنگی اونجا pause شده باشه، با اون قاطی میشه. پس یه alias جداگانه براش میسازم:

.. code:: bash
	  
   alias mpls='mplayer -input file=/media/fifo/mplfifo'

اینو میذارم توی zshrcم. الان میتونم آهنگ‌ها رو با mpls و فیلم‌ها رو با mplayer ببینم. از همین فایل پایپ برای کنترل ام‌پلیر از یه صفحه وب، که سرورش کامپیوتر خودمه، و البته فقط با شبکه‌ای که لپتاپم وصله میشه دیدش، استفاده میکنم.
