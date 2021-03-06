# php-03. Inquiry
________________________________________
## 1. Inquiry
________________________________________
composer.json

```json
{
    "require": {
        "phpmailer/phpmailer": "^6.0"
    }
}
```

Email.php

```php
<?php

namespace Php03;

use PHPMailer\PHPMailer\PHPMailer as PHPMailer;

/**
 * Email class.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */
class Email
{
    /**
     * PHPMailer for smtp.
     *
     * @var PHPMailer $smtp_
     */
    private $smtp_;

    /**
     * Constructor.
     *
     * @param string  $host    Host name.
     * @param integer $port    Port.
     * @param string  $user    User name.
     * @param string  $pass    Password.
     * @param bool    $isAuth  Uses auth. Default is true.
     * @param string  $sslType 'ssl' or 'tls'. Default is 'tls'.
     */
    public function __construct($host, $port, $user, $pass, $isAuth = true, $sslType = 'tls')
    {
        // Let's use PHPMailer.
        // composer require phpmailer/phpmailer
        $this->smtp_ = new PHPMailer();
        $this->smtp_->CharSet = "UTF-8";
        $this->smtp_->isSMTP();
        $this->smtp_->isHTML(false);
        $this->smtp_->Host = $host;
        $this->smtp_->Port = $port;
        $this->smtp_->Username = $user;
        $this->smtp_->Password = $pass;
        $this->smtp_->SMTPAuth = $isAuth;
        $this->smtp_->SMTPSecure = $sslType;
    }

    /**
     * Send e-mail.
     *
     * @param array  $toList   To list.
     * @param array  $ccList   CC list.
     * @param array  $bccList  Bcc list.
     * @param string $from     From.
     * @param string $fromName From name.
     * @param string $subject  Subject.
     * @param string $body     Body text.
     *
     * @return void
     */
    public function send($toList, $ccList, $bccList, $from, $fromName, $subject, $body)
    {
        // Clear error, to, cc, bcc, attached files.
        $this->smtp_->ErrorInfo = '';
        $this->smtp_->clearAllRecipients();
        $this->smtp_->clearAttachments();

        // Send
        foreach ($toList as $key => $to) {
            $this->smtp_->addAddress($to);
        }
        foreach ($ccList as $key => $cc) {
            $this->smtp_->addCC($cc);
        }
        foreach ($bccList as $key => $bcc) {
            $this->smtp_->addBcc($bcc);
        }
        $this->smtp_->From = $from;
        $this->smtp_->FromName = $fromName;
        $this->smtp_->Subject = mb_encode_mimeheader($subject); // Base64
        $this->smtp_->Body = $body;
        $this->smtp_->send();
        $errorInfo = $this->smtp_->ErrorInfo;
        if (!empty($errorInfo) && 0 < strlen($errorInfo)) {
            throw new \Exception($errorInfo);
        }
    }
}
```

inquiry-complete.html

```html
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8" />
    <title>Inquiry Complete</title>
</head>

<body>
    <div id="wrap">
        <header>
            <div>?????????????????????</div>
        </header>
        <main>
            <div>??????????????????????????????????????????????????????</div>
        </main>
        <footer>
            <div>Copyright ?? 2018 &lt;your name&gt; All Rights Reserved. </div>
        </footer>
    </div>
</body>

</html>
```

inquiry-confirmation.php

```php
<?php
/**
 * Inquiry sample's inquiry-confirm.php.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */

namespace Php03;

$name = $_POST['name'];
$email = $_POST['email'];
$sex = $_POST['sex'];
$inquiryType = $_POST['inquiry-type'];
$inquiryDetails = $_POST['inquiry-details'];
?>

<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8" />
    <title>Inquiry Confirmation</title>
</head>

<body>
    <div id="wrap">
        <header>
            <div>?????????????????????</div>
        </header>
        <main>
            <form method="POST" action="./register-inquiry.php">
                <fieldset>
                    <legend>????????????</legend>
                    <input type="hidden" name="name" value="<?php echo $name; ?>">
                    <input type="hidden" name="email" value="<?php echo $email; ?>">
                    <input type="hidden" name="sex" value="<?php echo $sex; ?>">
                    <input type="hidden" name="inquiry-type" value="<?php echo $inquiryType; ?>">
                    <input type="hidden" name="inquiry-details" value="<?php echo $inquiryDetails; ?>">
                    <div>
                        <span class="caption">??????</span>
                        <span class="colon">:</span>
                        <span class="input-text">
                            <?php echo $name; ?>
                        </span>
                    </div>
                    <div>
                        <span class="caption">?????????</span>
                        <span class="colon">:</span>
                        <span class="input-text">
                            <?php echo $email; ?>
                        </span>
                    </div>
                    <div>
                        <span class="caption">??????</span>
                        <span class="colon">:</span>
                        <span class="input-text">
                            <?php echo $sex; ?>
                        </span>
                    </div>
                    <div>
                        <span class="caption">????????????????????????</span>
                        <span class="colon">:</span>
                        <span class="input-text">
                            <?php echo $inquiryType; ?>
                        </span>
                    </div>
                    <div>
                        <span class="caption">????????????????????????</span>
                        <span class="colon">:</span>
                        <span class="input-text">
                            <?php echo $inquiryDetails; ?>
                        </span>
                    </div>
                </fieldset>
                <button type="submit" tabindex="70" accesskey="s">??????</button>
            </form>
        </main>
        <footer>
            <div>Copyright ?? 2018 &lt;your name&gt; All Rights Reserved. </div>
        </footer>
    </div>
</body>

</html>
```

inquiry-error.php

```php
<?php
/**
 * Inquiry sample's inquiry-confirm.php.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */

namespace Php03;

$errorText = $_GET['error-text'];
?>

<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8" />
    <title>Inquiry Error</title>
</head>

<body>
    <div id="wrap">
        <header>
            <div>????????????????????????</div>
        </header>
        <main>
            <div>
                <?php echo $errorText; ?>
            </div>
        </main>
        <footer>
            <div>Copyright ?? 2018 &lt;your name&gt; All Rights Reserved. </div>
        </footer>
    </div>
</body>

</html>
```

inquiry.html

```html
<!DOCTYPE html>
<html lang="ja">

<head>
    <meta charset="UTF-8" />
    <title>Inquiry</title>
</head>

<body>
    <div id="wrap">
        <header>
            <div>???????????????</div>
        </header>
        <main>
            <form method="POST" action="./inquiry-confirmation.php">
                <fieldset>
                    <legend>????????????</legend>
                    <input class="mode" type="hidden" name="mode">
                    <div class="input">
                        <label for="name">??????</label>
                        <input type="text" name="name" id="name" value="" tabindex="10" required="required" placeholder="??????????????????????????????">
                    </div>
                    <div class="input">
                        <label for="name">?????????</label>
                        <input type="email" name="email" id="email" value="" tabindex="20" required="required" placeholder="?????????????????????????????????????????????">
                    </div>
                    <div class="input">
                        <label for="sex-1">??????</label>
                        <input type="radio" name="sex" id="sex-1" value="???" tabindex="30" checked>
                        <label for="sex-1">???</label>
                        <input type="radio" name="sex" id="sex-2" value="???" tabindex="40">
                        <label for="sex-2">???</label>
                    </div>
                    <div class="input">
                        <label for="inquiry-type">????????????????????????</label>
                        <select name="inquiry-type" id="inquiry-type" tabindex="50">
                            <option value="????????????????????????1" selected="selected">????????????????????????1</option>
                            <option value="????????????????????????2">????????????????????????2</option>
                            <option value="????????????????????????3">????????????????????????3</option>
                        </select>
                    </div>
                    <div class="input">
                        <label for="inquiry-details">????????????????????????</label>
                        <textarea name="inquiry-details" id="inquiry-details" cols="30" rows="10" tabindex="60" accesskey="t" required="required">???????????????????????????????????????????</textarea>
                    </div>
                </fieldset>
                <button type="submit" tabindex="70" accesskey="s">??????</button>
            </form>
        </main>
        <footer>
            <div>Copyright ?? 2018 &lt;your name&gt; All Rights Reserved. </div>
        </footer>
    </div>
</body>

</html>
```

register-inquiry.php

```php
<?php
/**
 * Inquiry sample's inquiry-confirm.php.
 *
 * PHP Version 7.2
 *
 * @category Foo
 * @package  None
 * @author   takemori <foo@bar.baz>
 * @license  https://bar.baz/ MIT License
 * @link     None
 */

namespace Php03;

require_once "vendor/autoload.php";
require_once __DIR__.'/Email.php';

$name = $_POST['name'];
$email = $_POST['email'];
$sex = $_POST['sex'];
$inquiryType = $_POST['inquiry-type'];
$inquiryDetails = $_POST['inquiry-details'];

try {
    // Let's use mail trapping service.
    // https://mailtrap.io/
    $host = 'smtp.mailtrap.io';
    $port = 2525;
    $user = '7387aad7ba7380';
    $pass = '5537ae2f39e233';
    $toList = [$email];
    $from = 'user01@example.com';
    $fromName = '????????????01';
    $subject = '???????????????????????????????????????????????????????????????????????????????????????';
    $body = <<<EOT
??????????????????????????????????????????

???????????????????????????????????????????????????
??????????????????????????????????????????????????????????????????
EOT;
    $body .= "\n\n";
    $body .= '??????'."\n".$name."\n\n";
    $body .= '?????????'."\n".$email."\n\n";
    $body .= '??????'."\n".$sex."\n\n";
    $body .= '????????????????????????'."\n".$inquiryType."\n\n";
    $body .= '????????????????????????'."\n".$inquiryDetails."\n\n";

    $smtp = new Email($host, $port, $user, $pass);
    $smtp->send($toList, null, null, $from, $fromName, $subject, $body);
    $smtp->send(null, [$from], null, $from, $fromName, $subject, $body);
    $smtp->send(null, null, [$from], $from, $fromName, $subject, $body);
    header("Location: ./inquiry-complete.html");
} catch (\Exception $e) {
    header("Location: ./inquiry-error.php?error-text=".$e->getMessage());
}
```
