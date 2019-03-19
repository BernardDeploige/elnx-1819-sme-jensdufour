# Enterprise Linux Lab Report

- Student name: Jens Du Four
- Github repo: <https://github.com/HoGentTIN/elnx-1819-sme-jensdufourhogent.git>

Setting up a Ansible Vault for all unencrypted passwords in the assignment as mentioned in the previous reports.

## Test plan

### pr011

* Start the machine pr011 from a terminal with following command: `vagrant up pr011`
* Connect with the virtuele machine through SSH: `vagrant ssh pr011`
* Run following command to run the automated tests: `sudo /vagrant/test/runbats.sh`

### pu004

* Start the machine pu004 from a terminal with following command: `vagrant up pu004`
* Connect with the virtuele machine through SSH: `vagrant ssh pu004`
* Run following command to run the automated tests: `sudo /vagrant/test/runbats.sh`

## Procedure/Documentation
The following servers use unencrypted passwords in their configuration:

* pr011
* pu004

All other passwords used throughout the assignment were generated with [MKPasswd](https://www.mkpasswd.net/index.php), `sha512-crypt` was used as encryption in those passwords.

### Configuration for `pu004`
The unencrypted passwords are to be encrypted using the following commando from the project directory:

    ansible-vault encrypt_string $password123 

This command will prompt for a password used in the encryption. In this assignment the password `project` was chosen. This is required during the provisioning. 

**Encryption of `pu004`**

For the MariaDB-user:

    password: !vault |
            $ANSIBLE_VAULT;1.1;AES256
            32303934383036653535653436326237336138316230656366336531633533383135626538343133
            6636373530646533346135303763646439336564666461380a653434343136366130333964653337
            63323139616633653235333331323234656337396232343462353931613137353735366164616233
            3234623236356232660a363830623237326637663363373863633838646262333934303162333539
            3239


For the MariaDB-root:

    mariadb_root_password: !vault |
            $ANSIBLE_VAULT;1.1;AES256
            62353837623765613033303165393635623832393137663964643039326237333632396266323662
            3732656338363566323134613766303135386132376161310a646130326530383938346661353762
            34613035303664626461663030383166353732623161313938653932626239383666333363373033
            3431393364346532380a663564623632343061636234323663616565636663363861356264653862
            6663

For the WordPress-password:

    wordpress_password: !vault |
            $ANSIBLE_VAULT;1.1;AES256
            32303934383036653535653436326237336138316230656366336531633533383135626538343133
            6636373530646533346135303763646439336564666461380a653434343136366130333964653337
            63323139616633653235333331323234656337396232343462353931613137353735366164616233
            3234623236356232660a363830623237326637663363373863633838646262333934303162333539
            3239

### Configuration for `pr011`
The unencrypted passwords are to be encrypted using the following commando from the project directory:

    ansible-vault encrypt_string $password123 

This command will prompt for a password used in the encryption. In this assignment the password `project` was chosen. This is required during the provisioning. 

**Encrypteren van de gebruikte paswoorden**

For user `stevenh`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          38396138623064393338313632643832623561336261633565633464353533623032653032613238
          3865653539636664653063396630366666356330656338360a646339646436623233303565366535
          61303534663730343133383135396438663331616331643939393565386431306136323066373831
          6436376365383838620a363730326330636362323163313734323261316562376334313864323565
          6535

For user `stevenv`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          37653735363137333364326139363634626636323365303630663364613831303430613736323633
          6461643538333336656266353337663061663937666331300a343130656162336366633435613039
          33373939613034633539633963623636396238633662656363346637373030326632333033383030
          3865393938653362350a643533376366643733366661643230373938636132636165323639313030
          3834

For user `leend`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          31663733376631376539353538663638396430396365343437326633633261336236373661376133
          3431336366393164636330623165623862313965373131380a303532313136616134636435366565
          65663862306230626464303638313062663334323031653561633034386230636336653338306466
          3762383962656331310a616566306134653364316436636466646533396134323465393065613363
          6233

For user `svena`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          36386539633566636132306566363336616365353165373934386265633131326334336139616137
          3035643133356434343831353361363630346536323334610a626437373362323639333134333836
          63643862373633343734313539326338646239656362303937323561386634656663626336626639
          3063303837316535350a613334646637306162303462636438636530646332653432613731663735
          6361

For user `nehirb`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          63616562663935313439343334656430643135386431383465643332666366336533363962306662
          3933383664623539643334633338396539353633383362360a613034393365663637666661653633
          30313639313830616361343339636163633663613032313036666532663933343465356436643332
          3336616665623365390a623534393766366662306532383830626637373164643266346564656235
          3639

For user `alexanderd`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          34313061393061303532646131333665306437666437316132313666393636333239636266336133
          3132383133396365336364383261633763383864383563640a303036653433616461656534316632
          65653233646239383963643530346538663033656533333163666133383439363339303735396663
          3331396635346363370a626465623737333036306230386437303364343134613538653366653864
          3964

For user `krisv`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          64303435626432353961343631616631383039633337363533613162653432353138333931383665
          3634336166366431393461336533666638363237656330310a356439363566643737636264363561
          64356137613765356265396264386235613633353962653061333161333964613665663138353733
          3263333431663033620a393736326362656336333564353638623366666463346335383366363335
          3065

For user `benoitp`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          37353233313437656263323161666132616339613163653532643534666562646433656266303336
          3166653665313366303433326334363031623534343432360a633137366437323061353366663738
          64653439666565356161656234653666373731333230663938626236383338656431636639363038
          3133646637383737360a633832636231626437303430303131353535373036316132653630316331
          3763

For user `anc`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          39383366663233623766663064613265316131396131643963306566346333616236376232396335
          3838376435323539613564656462313831383935656363310a303432313664383166386339333434
          62626330333530306533393733386632356237303663323035623738383661643466616563323933
          6631663062633030340a313661653531306163343966633533363363356637363462623030636231
          3061

For user `elenaa`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          61613138323964343133326537373737356334396538623232303861613163366634303738303065
          6230363537316639653631356133326331313661623935350a356561383765306361663062386265
          64383332303933643466383436383662346330323863333864663561383632613331333966336637
          3365343763366563330a316163633430313161343131663865363162333738643332633538326364
          3139

For user `evyt`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          32373239363564646430313134386265623764376365313835346464653962363936303962373061
          3831663062303039646231613938636239333839353062310a636665306363353765366531366434
          31623364623231363863396563343865343134383037326533613338633661643063323632313165
          3837306131366533380a336533633736623837333062643337313062326265383163626665333864
          6238

For user `christophev`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          64666364646239323762326361383065643365343236373766666364643162613563613663316662
          3538373931353663303938313832386236333765333132370a616236376662666438343736316134
          39356135373733376233623166623731663431373061626538393566353535353636633935316162
          6266316463313766320a343662346239346533323533306431333166663234623166373132386135
          3965

For user `stefaanv`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          64666638343031376362306536336462323930323766663837326636316230346535346437363838
          3537386362626333663937306230633539306463383135330a373463333562343365316262333864
          63376464363266663536633039373361393865666361393331366563353835346363653539366435
          3663613261626233640a343333376231636134363630653039346434326533353461633635623334
          3330

For user `jens`:

    password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          33656134643931353166616538376164336565313665373537383863346536313234663139666631
          3836383838316462656636656562316230356163323633310a373963643936653938383461376136
          31303833613565646566356431646237366131326339373938636438663964336666336231393936
          3566643665613264330a616337363666326162393130626630643236613130343234333036353930
          3161            


## Test report

After following the test plan the following output should be returned for respectively each server.

### pr011
    Running test /vagrant/test/common.bats
     ✓ EPEL repository should be available
     ✓ Bash-completion should have been installed
     ✓ bind-utils should have been installed
     ✓ Git should have been installed
     ✓ Nano should have been installed
     ✓ Tree should have been installed
     ✓ Vim-enhanced should have been installed
     ✓ Wget should have been installed
     ✓ Admin user jens should exist
     ✓ Custom /etc/motd should have been installed

    10 tests, 0 failures
    Running test /vagrant/test/pr011/samba.bats
     ✓ The ’nmblookup’ command should be installed
     ✓ The ’smbclient’ command should be installed
     ✓ The Samba service should be running
     ✓ The Samba service should be enabled at boot
     ✓ The WinBind service should be running
     ✓ The WinBind service should be enabled at boot
     ✓ The SELinux status should be ‘enforcing’
     ✓ Samba traffic should pass through the firewall
     ✓ Check existence of users
     ✓ Checks shell access of users
     ✓ Samba configuration should be syntactically correct
     ✓ NetBIOS name resolution should work
     ✓ read access for share ‘public’
     ✓ write access for share ‘public’
     ✓ read access for share ‘management’
     ✓ write access for share ‘management’
     ✓ read access for share ‘technical’
     ✓ write access for share ‘technical’
     ✓ read access for share ‘sales’
     ✓ write access for share ‘sales’
     ✓ read access for share ‘it’
     ✓ write access for share ‘it’

    22 tests, 0 failures
    Running test /vagrant/test/pr011/vsftp.bats
    ✓ VSFTPD service should be running
    ✓ VSFTPD service should be enabled at boot
    ✓ The ’curl’ command should be installed
    ✓ The SELinux status should be ‘enforcing’
    ✓ FTP traffic should pass through the firewall
    ✓ VSFTPD configuration should be syntactically correct
    ✓ Anonymous user should not be able to see shares
    ✓ read access for share ‘public’
    ✓ write access for share ‘public’
    ✓ read access for share ‘management’
    ✓ write access for share ‘management’
    ✓ read access for share ‘technical’
    ✓ write access for share ‘technical’
    ✓ read access for share ‘sales’
    ✓ write access for share ‘sales’
    ✓ read access for share ‘it’
    ✓ write access for share ‘it’

### pu004

    Running test /vagrant/test/common.bats
     ✓ EPEL repository should be available
     ✓ Bash-completion should have been installed
     ✓ bind-utils should have been installed
     ✓ Git should have been installed
     ✓ Nano should have been installed
     ✓ Tree should have been installed
     ✓ Vim-enhanced should have been installed
     ✓ Wget should have been installed
     ✓ Admin user jens should exist
     ✓ Custom /etc/motd should have been installed

    10 tests, 0 failures
    Running test /vagrant/test/pu004/lamp.bats
     ✓ The necessary packages should be installed
     ✓ The Apache service should be running
     ✓ The Apache service should be started at boot
     ✓ The MariaDB service should be running
     ✓ The MariaDB service should be started at boot
     ✓ The SELinux status should be ‘enforcing’
     ✓ Web traffic should pass through the firewall
     ✓ Mariadb should have a database for Wordpress
     ✓ The MariaDB user should have "write access" to the database
     ✓ The website should be accessible through HTTP
     ✓ The website should be accessible through HTTPS
     ✓ The certificate should not be the default one
     ✓ The Wordpress install page should be visible under http://192.0.2.50/wordpress/
     ✓ MariaDB should not have a test database
     ✓ MariaDB should not have anonymous users

    15 tests, 0 failures

## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.

[Ansible Vault](https://docs.ansible.com/ansible/2.4/vault.html)

[Vagrant: Ansible Vault](https://www.vagrantup.com/docs/provisioning/ansible.html#ask_vault_pass)

[Stack Overflow](https://stackoverflow.com/questions/30209062/ansible-how-to-encrypt-some-variables-in-an-inventory-file-in-a-separate-vault)
