[FreeHackQuest](https://freehackquest.com/quest/107)
Описание:
Получить доступ к почте администратора.
[corp_mail.apk](https://freehackquest.com/public/files/quests/EF812732-060E-4C14-506B-38147D9FA69F_corp_mail.apk)

``` Java
    @Override // android.support.v7.app.AppCompatActivity, android.support.v4.app.FragmentActivity, android.support.v4.app.BaseFragmentActivityDonut, android.app.Activity
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        this.et_Username = (EditText) findViewById(R.id.et_Username);
        this.et_Password = (EditText) findViewById(R.id.et_Password);
        this.bt_SignIn = (Button) findViewById(R.id.bt_SignIn);
        this.tv_Message = (TextView) findViewById(R.id.tv_Message);
        this.bt_SignIn.setOnClickListener(new View.OnClickListener() { // from class: su.keva.revapp.Main.1
            @Override // android.view.View.OnClickListener
            public void onClick(View view) {
                String email = String.valueOf(Main.this.et_Username.getText());
                String password = String.valueOf(Main.this.et_Password.getText());
                if (TextUtils.isEmpty(email)) {
                    Main.this.tv_Message.setText("Email is empty!");
                    return;
                }
                if (!Main.this.isEmailValid(email)) {
                    Main.this.tv_Message.setText("Email is invalid!");
                    return;
                }
                if (!TextUtils.isEmpty(password) && !Main.this.isPasswordValid(password)) {
                    Main.this.tv_Message.setText("Password is empty, or too short!");
                } else if (email.equals("admin@avek.com") && Main.this.hash(password).equals("7eaefcd858eaafc0a618a3817748838597b147c6")) {
                    Main.this.tv_Message.setText("Login Successful!");
                } else {
                    Main.this.tv_Message.setText("Login Unsuccessful!\n Try Hint: \\w{2}_\\w{2}");
                }
            }
        });
    }

    public String hash(String s) throws NoSuchAlgorithmException {
        try {
            MessageDigest digest = MessageDigest.getInstance("SHA");
            digest.update(s.getBytes());
            byte[] messageDigest = digest.digest();
            StringBuffer hexString = new StringBuffer();
            for (byte b : messageDigest) {
                hexString.append(Integer.toHexString(b & 255));
            }
            return hexString.toString();
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
            return "";
        }
    }
```

Пароль можно просто забрутфорсить через hashcat:
``` bash
./hashcat -m 100 -a 3 7eaefcd858eaafc0a618a3817748838597b147c6 --increment --increment-min 1 ?a?a?a?a?a?a?a?a                        
```

![{7021AF2F-A24B-4FBA-AAF6-828700A02AB7}](../../../z.%20Images/{7021AF2F-A24B-4FBA-AAF6-828700A02AB7}.png)

Ответ: `sh_ps`