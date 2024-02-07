## ரெடிஸ் வரிசைப்பட்டி

ரெடிஸ் அடிப்படையில் உள்ள செய்தி வரிசைப்பட்டி, செய்தி தாமதம் செயல்படுத்த ஆதரிக்கிறது.

## நிறுவுதல்
`composer require webman/redis-queue`

## கட்டமைப்பு கோப்பு
Redis கட்டமைப்பு கோப்பு தாராளமாக `config/plugin/webman/redis-queue/redis.php` இல் தானாக உருவாகிறது, உருப்படியான உள்ளடக்கம் போன்றவை கீழே உள்ளன:
```php
<?php
return [
    'default' => [
        'host' => 'redis://127.0.0.1:6379',
        'options' => [
            'auth' => '',         // கடவுச்சொல், விருப்பமானவர்
            'db' => 0,            // தரவுத்தளம்
            'max_attempts'  => 5, // செயல்முறை தோல்வியடைந்த பிறகு, மீண்டும் முயல் செய்தல் முயல்கிறது
            'retry_seconds' => 5, // முயல் இடங்களின் இடவேளை, நேரம் வினித்து
        ]
    ],
];
```

### செயல்முறை தோல்வி மீண்டும் முயல்கின்றது
செயல்முறை தோல்வி ஏற்பட்டால் (விபரப்படுத்துறது), அந்த செய்தி வரிசையில் நிதி சேர்க்கப்படும், முயல் வரப்பில் காத்திருக்கும். முயல் வரத்தின் முயல் இடைக்குச் செல்லும் நேரம் `retry_seconds` மற்றும் `max_attempts` கூட்டமைப்பின் மூலம் படுக்கின்றது. உதாரணமாக, `max_attempts` ஐ 5 ஆகப் பார்த்துக்கொண்டு, `retry_seconds` ஐ 10 ஆக மாற்றினால், முதல் முயல் இடைக்கு `1*10` விநாடிகள், இரண்டாம் முயல் இட்டால் `2*10` விநாடிகள், மூன்றாம் முயல் இடுகை `3*10` பிளவுகளாக இருக்கும், ஒதுக்கமாக பதிவு செய்யப்படுகிறது 5 முயல்கள் வரை. மேலும் `max_attempts` அமைக்கவும் முயலாவின் வெற்றுவாய்விட அமைந்தால், அப்படியே செய்தி `{redis-queue}-failed` என்பது தோல்வி வரிசையில் வைக்கப்படும்.
## செருகுவிப்பது (ஒப்புவழி)
> **குறிப்பு**
> webman / redis >= 1.2.0 தேவை, பொருநரவற்றிடத்தில் நிரல் விளைவு

```php
<?php
namespace app\controller;

use support\Request;
use Webman\RedisQueue\Redis;

class Index
{
    public function queue(Request $request)
    {
        // பத்திரக்கூடியின் பெயர்
        $பத்திரக்கூடியுக்கு = 'மின்னஞ்சல்குழுவிற்கு';
        // தரவு, உரைவரிசைப்படுத்த தேவைப்படாது பட்டியலைத் தரவுத்தளத்தில் அனுப்பலாம்
        $தரவு = ['to' => 'tom@gmail.com', 'content' => 'hello'];
        // செருகுவிப்பது
        Redis::send($பத்திரக்கூடியுக்கு, $தரவு);
        // தாமதவரிடத்தில் செருகுவிப்பது, செயல்படுத்த ஒரு நிமிடத்திற்குள் அனுப்பப்படும்
        Redis::send($பத்திரக்கூடியுக்கு, $தரவு, 60);

        return response('ரெடிஸ் பத்திரக்கூடி சோதனை');
    }

}
```
செருகுவிப்பது வெற்றிகரமாக இருந்தால் `Redis::send()` முடிவுகள் மற்றும் பிழை வரவிருக்கும் அல்லது வெளிப்படுத்தப்படும்.

> **பரிசோதனை**
> தாமதப்படுத்தப்படும் பத்திரக்கூடி முகவரியத்திற்கான செல்லுபடியாக பிழை ஏற்படலாம், உதவி நிறைவடைய முறையாக இனி செலவு வேகம் தாமதப்படுத்துவதன் மூலம் தடுப்பது.
செய்தி அயக்கம் (வேலை மறுக்கப்படும்)

```php
<?php
namespace app\controller;

use support\Request;
use Webman\RedisQueue\Client;

class Index
{
    public function queue(Request $request)
    {
        // துறை பெயர்
        $queue = 'send-mail';
        // தரவுகள், படிக்கும் பெருமகிவுமான விருப்பமுள்ளது, ஒரு அண்மையில் மூலம் செலுத்த வேண்டும்
        $data = ['to' => 'tom@gmail.com', 'content' => 'hello'];
        // செய்கிற செய்தி வழங்கும்
        Client::send($queue, $data);
        // தாமதமாகவே செய்தி அனுப்பும், செங்கிலும் பிப்ரவரி ரீதியில் மாதம் ஆண்டு பொதுவாக மாறும்
        Client::send($queue, $data, 60);

        return response('ரெடிஸ் இடுகை சோதனை');
    }

}
```

`Client::send()` ஒரு முடிவுற்ற முறையில் திரும்பவில்லை, அது விரிக்கின்றது என அத்துடன் ரெடிஸில் செலுத்தப்படுகின்ற அளவுகோப்பை சாத்தியமில்லை.

> குறிப்பு
> `Client::send()` மூல என்னும் ஒரு இயல்பு எழுத்துக்களத்திற்கு ரெடிஸில் செலுத்தப்படுகின்றது, இது பொறுமையில் 100% செயல்பாடு ரெடிஸ்க்கு அனுப்ப மாட்டாது.
 
> குறிப்பு
> `Client::send()` என்பது அழகானோருக்கு திரும்ப வேண்டியது, அது workerman அமர்வு சூழலில் மட்டுமே பயன்படுத்த முடியும், வழக்கமான சிட்ரிப்ட் பங்குகளுக்கு மேல்வெளியே அழகியமான படைப்பு `Redis::send()` ஐ பயன்படுத்தவும்.
பிற திட்டத்தில் செய்திகளை பகிருங்கள்
செயலானது `webman\redis-queue` ஐ பயன்படுத்த முடியாது என்றால், தொடர்ந்து வரவேற்புகளை பக்கத்திற்குக் கொண்டுள்ள குறிப்புகளினைப் பார்வையிட முடியும். 

```php
function redis_queue_send($redis, $queue, $data, $delay = 0) {
    $queue_waiting = '{redis-வரிசை}-காதில்';
    $queue_delay = '{redis-வரிசை}-தாமதம்';
    $now = time();
    $package_str = json_encode([
        'id'       => rand(),
        'time'     => $now,
        'delay'    => $delay,
        'attempts' => 0,
        'queue'    => $queue,
        'data'     => $data
    ]);
    if ($delay) {
        return $redis->zAdd($queue_delay, $now + $delay, $package_str);
    }
    return $redis->lPush($queue_waiting.$queue, $package_str);
}
```

இங்கே, `$redis` மாணவர் ஆகும் ரெடிஸ் நிலையத்தைக் குறிப்பிடுக. உதாரணமாக redis நிறுவும் மொழிவைக்கான பதிவு :

```php
$redis = new Redis;
$redis->connect('127.0.0.1', 6379);
$queue = 'user-1';
$data= ['சில', 'தரவு'];
redis_queue_send($redis, $queue, $data);
````
## சேமிப்பு
சேமிப்பு மூலங்கள் கோப்பில் `config / plugin / webman / redis-queue / process.php` இல் உள்ளன.
சேமிப்பாளர் அடைவு `app / queue / redis /` கீழே உள்ளது.

`php webman redis-queue:consumer my-send-mail` கட்டளையை இயக்கினால் `app / queue / redis / MyMailSend.php` கோப்பு உருவாகியுள்ளது.

> ** குரிப்பு**
> கட்டளை இல்லாமல் அப்படியே கைமுறையாக உருவாக்கலாம்.

```php
<?php

namespace app\queue\redis;

use Webman\RedisQueue\Consumer;

class MyMailSend implements Consumer
{
    // சேமிப்பு பெயர்
    public $queue = 'send-mail';

    // இணைப்பு பெயர், 'plugin / webman / redis-queue / redis.php' இல் உள்ள இணைப்பு`
    public $connection = 'default';

    // சேமி
    public function consume($data)
    {
        // திருத்துவத்திற்கு தேவையானதில்லை
        var_export($data); // வெளியீடு ['to' => 'tom@gmail.com', 'content' => 'hello']
    }
}
```

> **குறிப்பு**
> சேமை செய்யும் போது சவால் அல்லாதியாக அல்லது பிழை ஏற்பட்டபோது, சேமையில் வெற்றியாகாவிட்டால், புதிய முயற்சியில் உள்ளதாகும். போதுமானது பிழை ஏற்பட்டால் தற்போதும் முயற்சியில் உள்ள அத்தாட்சியைச் சுட்டி செலுத்த, முனையாக இது நிறுவப்பட்டுள்ளது. வாங்கிய வழியில், இது அதனால் ஒரு சத்தியப் பரிமாற்றங்களை உருவாக்குகின்றது.

> **குரிப்பு**
> சேமிப்பு முனைகள் பல சேவையகங்கள் பல முயன்று சேமிக்க முடியும், மேலும் அதே செயலியை மீட்பு செய்யவில்லை. சேமிக்கப்பட்டவை மேலேயுள்ளவையாகும், அதை கைமுறையில் நீக்க தேவையில்லை.

> **குரிப்பு**
> விண்டோஸ் பயனர்கள் 'php windows.php' ஐ இயக்கும் போது webman ஐ தொடங்க, இல்லையானால் சேமிப்பு முனுக்கினை துவக்க முடியாது.
இயல்பான நிரல்பாகத்திற்கு, அனைத்து உணவினர்களும் ஒரே அனைத்து உணவினரும் பயன்படுத்துகின்றன. ஆனால் சில நேரங்களில் கொஞ்சம் வேலை செய்யும் பயிற்சியை ஒரு குழுவில் சேர்க்க வேண்டும், உதாரணமாக வேலை மெதுவான வாய்ப்பை ஏற்படுத்தவும், வேலை நெட்டுக்கும் வேலையை வாங்குவது வேலை வேலையாக ஏற்படுகிறது. இதற்கான செயல்முறையைத் தருவதற்கான பொருட்களில் நிரல் உணவினர்களை இருப்பிடமிருக்கும் படைப்புகளை, உருப்படியான உறுப்பினர் பணியில்தங்குகின்றனர், எனவே அதைப் பிரிக்கவோ அல்லவோ செய்ய முடியும்.
```php
return [
  ...மற்ற கட்டமைப்புகளை இதுவரைவிடாமை...
    
    'redis_consumer_fast'  => [
        'handler'     => Webman\RedisQueue\Process\Consumer::class,
        'count'       => 8,
        'constructor' => [
            // உணவினர் குழு
            'consumer_dir' => app_path() . '/queue/redis/fast'
        ]
    ],
    'redis_consumer_slow'  => [
        'handler'     => Webman\RedisQueue\Process\Consumer::class,
        'count'       => 8,
        'constructor' => [
            // உணவினர் குழு
            'consumer_dir' => app_path() . '/queue/redis/slow'
        ]
    ]
];
```

மூலம் உறுப்பினர் வகைகளைத் தர மற்றும் அதனுடன் இணைக்கப்பட்ட செயல்முறையைக் கட்டுப்படுத்தி, நாம் எளிதாக வேலையாளரின் வேலையை அமைப்பதுடன் வேலைவாய்ப்பை ஏற்படுத்தலாம்.
## பல ரெடிஸ் கட்டமைப்புகள்
#### கட்டமைப்பு
`config/plugin/webman/redis-queue/redis.php`
```php
<?php
return [
    'default' => [
        'host' => 'redis://192.168.0.1:6379',
        'options' => [
            'auth' => null,       // கடவுச்சொல், துணிப்புகள் வகையான விருப்பங்கள்
            'db' => 0,            // தரவுத்தளம்
            'max_attempts'  => 5, // விரும்பிப் போன்றியின்பம் நிறுத்தப்பட்ட பின், மீண்டும் முயற்சி முடியும் முயன்றால் முடியும்
            'retry_seconds' => 5, // முயற்சி இடம்பெயர்வழி, நேரங்கள்
        ]
    ],
    'other' => [
        'host' => 'redis://192.168.0.2:6379',
        'options' => [
            'auth' => null,       // கடவுச்சொல், துணிப்புகள் வகையான விருப்பங்கள்
            'db' => 0,             // தரவுத்தளம்
            'max_attempts'  => 5, // விரும்பிப் போன்றியின்பம் நிறுத்தப்பட்ட பின், மீண்டும் முயற்சி முடியும் முயன்றால் முடியும்
            'retry_seconds' => 5, // முயற்சி இடம்பெயர்வழி, நேரங்கள்
        ]
    ],
];
```

கட்டமைக்கப்பட்ட படிவத்தில் `other` என்ற ஒரு முக்கியமான ரெடிஸ் கட்டமைப்பு சேர்க்கப்பட்டுள்ளது

#### பல ரெடிஸ் செய்தி அனுப்பல்

```php
// `default` என்ற முக்கியமான பூஜ்ஜியில் செய்தி ஒன்றைப் பதிவு செய்க
Client::connection('default')->send($queue, $data);
Redis::connection('default')->send($queue, $data);
// இதுவரை
Client::send($queue, $data);
Redis::send($queue, $data);

// `other` என்ற முக்கியமான பூஜ்ஜியில் செய்தி ஒன்றைப் பதிவு செய்க
Client::connection('other')->send($queue, $data);
Redis::connection('other')->send($queue, $data);
```

####  பல ரெடிஸ் செலவித்தல்

`others` என்ற முக்கியமான பூஜ்ஜியில் செய்தி ஒன்றைப் பதிவு செய்க
```php
namespace app\queue\redis;

use Webman\RedisQueue\Consumer;

class SendMail implements Consumer
{
    // செலவிடலாகும் வரையறை பெயர்
    public $queue = 'அஞ்சலை அனுப்பு';

    // === இங்கு other என்னும், முன்னிருந்ததை ரெடிஸ் அமைப்பில் ஒப்புக்கொள்கின்றன ===
    public $connection = 'other';

    // செலவு செய்ய
    public function consume($data)
    {
        // எதுவும் மாற்ற தேவையில்லை
        var_export($data);
    }
}
```
## பொதுவான வினவல்கள்

**"Workerman\Redis\Exception: Workerman Redis காத்திருக்கும் காலம் (600 நொடிகள்)" என்பது ஏன் ஏற்படுகிறது?**

இந்த பிழை மட்டும் அறிக்கை`Client::send()` நிரப்பும் அல்லதுப் பிரிந்துகொள்ளும் முறையில் மட்டுமே ஏற்படும். பிரமிப்பாக, முறையை முழுவதுமாக ஆனது அசர்வம் புதிய நினைவகத்தில் தற்காலிகமாக சேமிக்கப்படும், ப்ரோஸெஸ் சீர்களையும் சுழற்றுகின்று, பிரமிப்புகளை புதுக்கப்படுத்துகிறது. எனவே, செயற்கைக்கும் அன்ன வணிகங்களில் அன்பு இல்லாமல் அல்லது முடக்கங்களில் செயல்பட்டுள்ளது, மட்டும் மக்களின் நினைவகத்தை ரெடிஸ்இல் இடுக்கைக்கு அனுப்பிக்காது, இது வெற்றிகரமாகச் செல்லும். அனுப்பத்தக்க தரவை இடுக்கைக்குக் குறையாக்குவதற்கான 600 நொடிக்குள் மீதமாக மட்டுமே ஏற்படும்.

பிரசாரப்படுத்துவது தீங்கில், ஆனது புதுக்கப்படுத்தும் முறையைப் பயன்படுத்துக்கவும்`Redis::send()`.