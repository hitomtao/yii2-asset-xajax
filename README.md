# yii2-asset-xajax
Xajax for yii2 framework

Installation
------------

```
composer require tsigularov/yii2-asset-xajax @dev
```
make asset file @assets/XajaxAsset.php

```php

namespace app\assets;

use Yii;
use yii\web\AssetBundle;
use yii\web\View;


class XajaxAsset extends AssetBundle
{
    public $sourcePath = '@vendor/tsigularov/yii2-asset-xajax/assets';

    public $js = [
//        'js/xajax_core.js',
    ];
}

```

make components file @app/components/XajaxComponent

```php

namespace app\components;

use app\assets\XajaxAsset;
use \Yii;
use app\modules\warehouse\components\Url;
use yii\web\YiiAsset;

class XajaxComponent extends \yii\base\Component
{
    public $xajax_string;

    public $xajax_load = false;

    public function init()
    {

    }
    public function start($params){

        require_once Yii::getAlias('@vendor/tsigularov/yii2-asset-xajax/src/xajaxAIO.inc.php');
        $this->xajax_string = new \xajax();

        foreach ($params as $val) {
            $this->xajax_string->registerFunction(array($val[0], &$val[1], $val[2]));
        }


        $this->xajax_string->configure('debug', false);


        $this->xajax_string->configure('characterEncoding', 'utf-8');
        $this->xajax_string->configure('decodeUTF8Input', true);
        $this->xajax_string->configure('cleanBuffer', true);

        $this->xajax_load = true;

        $this->xajax_string->processRequest();

    }

    public function printJavascript()
    {
        if ($this->xajax_load) {
            $this->xajax_string->printJavascript(Yii::$app->view->assetBundles[XajaxAsset::className()]->baseUrl."/js/");
        }
    }
}

```

Start xajax component in any controller of your project

```php

    public function init()
    {
        $params = array();
        $params[] = array("callBack",$this,"callBack");
        Yii::$app->xajax->start($params);
    }

```
add callBack function in the same controller

```php

    public function callBack(){
        $response = new \xajaxResponse();

	$response->alert('This call back from server');

        return $response;
    }

```

Add print function in header of your project for example @view/header

```php
   Yii::$app->xajax->printJavascript();

```