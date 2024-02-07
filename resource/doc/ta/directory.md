# கோப்புக் கட்டமை

``` 
.
├── app                           பயன்பாடு கோப்புறை
│   ├── controller                கட்டுப்பாடு கோப்புறை
│   ├── model                     மாதிரிக்கை கோப்புறை
│   ├── view                      பார்வை கோப்புறை
│   ├── middleware                மத்திய கோப்புறை
│   │   └── StaticFile.php        இயலுமை நிலை கோப்பு மத்தியம்
|   └── functions.php             வணிக பிரதியேக செயலிகளை இந்த கோப்பில் எழுதுவது
|
├── config                        உள்ளமை கோப்புறை
│   ├── app.php                   பயன்பாடு உள்ளமை
│   ├── autoload.php              இங்கு உள்ள கோப்புகள் தானாகவே ஏற்றப்படும்
│   ├── bootstrap.php             பூட்டல் ஏற்றுமதி பேரன்புங்கள் பீத்தியேனவும் 
│   ├── container.php             உறுப்பின் கட்டமை
│   ├── dependence.php            உறுப்பின் உடன் மீது 
│   ├── database.php              தரவுத்தளம் உள்ளமை
│   ├── exception.php             விலகியுள்ளமை
│   ├── log.php                   பதிவு உள்ளமை
│   ├── middleware.php            மத்திய உள்ளமை
│   ├── process.php               தனிப்பயனாக்க பெயர்வு உள்ளமை
│   ├── redis.php                 எரித்தம் உள்ளமை
│   ├── route.php                 வழி உள்ளமை
│   ├── server.php                வரவு, பூட்சஸ் இலக்கம்பருக் தேர்வு உள்ளமை
│   ├── view.php                  காட்சி உள்ளமை
│   ├── static.php                நிலை கோப்பு மற்றும் நிலை கோப்பு மத்தியம் உள்ளமை
│   ├── translation.php           பல மொழி உள்ளமை
│   └── session.php               அமர்வு உள்ளமை
├── public                        நிலைக் கருவி கோப்புறை
├── process                       தனிப்பயனாக்க பெயர்வு கோப்புறை
├── runtime                       பயன்பாடு இயங்குகிற நேயா கோப்புறை, எழுதல் அனுமதி வேண்டும்
├── start.php                     சேவை ஆரம்ப கோப்பு
├── vendor                        ஆலமரி நிறுவல் சனாப்பு கோப்புறை
└── support                       வரையறுக்காட்சி (மூல நிறைய பொருட்கள் இல்லாதது)
    ├── Request.php               கேள்வி வகை
    ├── Response.php              பிரதிகர வகை
    ├── Plugin.php                சேரல், நீக்கல் நன்மை
    ├── helpers.php               உதவி செயல்பாடுகள் (வணிக தனி பிரதியேக செயலிகளை app/functions.php இல் எழுதுங்கள்)
    └── bootstrap.php             பார்வை மற்றும் பயன்பாட பிரிவு திறனிற்கு பிரிவு
```