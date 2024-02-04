
# Fuel Beta-4 Testnet Node Kurma Rehberi
![Adsız (Sunum)](https://github.com/eminmtas/fuel-beta4-node-kurma-rehberi/assets/44838743/78117d0a-1c9e-45ba-a616-b1a64434d74e)

Selamlar, [Fuel](https://x.com/fuel_network) Beta-4 testneti için kolay bir şekilde node kurabilmeniz için küçük bir rehber hazırlamak istedim. Orijinal dokümantasyon kafa karıştırdığı için basite indirgemeye çalıştım. Umarım açıklayıcı olmuşumdur.

Öncelikle sağ üstten yıldızlamayı ve forklamayı unutmayalım. Thx.

**Ödülsüz olacağını [açıklamışlar](https://fuel-labs.ghost.io/announcing-beta-4-testnet/#:~:text=Please%20note%20that%C2%A0beta%2D4%C2%A0is%20not%20an%20incentivized%20testnet.%20There%20will%20be%20no%20direct%20rewards%20for%20participation%20in%20this%20network). Kurup kurmamak size kalmış.**

| Donanım    | Minimum  | Tavsiye Edilen |
|------------|----------|-----------------|
| İşlemci    | 2 Çekirdek | 8 Çekirdek     |
| Bellek     | 4 GB     | 12 GB           |
| Depolama   | 30 GB    | 100 GB          |

Sanal sunucu almak için Hetzner, Contabo ya da AWS gibi sanal makine sağlayıcılarını kullanabilirsiniz. Ücretsiz Amazon sunucunuza bile kurabilirsiniz.

## Öncelikle Rust Yüklüyoruz

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```
Birkaç seçenek çıkacak. 1 yazıp enterlıyoruz.

Kurulum bittikten sonra şu kodu kullanın:
```bash
$HOME/.cargo/env
```

## Fuel Servislerini Yüklüyoruz

```bash
curl --proto '=https' --tlsv1.2 -sSf https://install.fuel.network/fuelup-init.sh | sh
```

Kurulum bittikten sonra çıktıdaki `source` ile başlayan kodu kopyala yapıştır yapıp enterlıyoruz. Bunu yapmazsanız habire hata alırsınız!

## Beta-4 Testneti için Gerekli Servisleri Yüklüyoruz

```bash
fuelup toolchain install beta-4
```

Beta-4'e node kuracağımız için aşağıdaki kodu da girelim
```bash
fuelup default beta-4
```

## Alchemy ya da Infura'dan API Key/RPC alıyoruz

Herhangi bir RPC sağlayıcısından da ücretsiz bir şekilde Sepolia için API Key/RPC alabilirsiniz. Aldığınız RPC şu şekilde görünecek:

```bash
#Infura
https://sepolia.infura.io/v3/{API_KEYİNİZ}
#Alchemy
https://eth-sepolia.g.alchemy.com/v2/{API_KEYİNİZ}
```

RPC'nizi aldıktan sonra bir yere kaydedin, az sonra bunu kullanacağız.

## P2P Key üretiyoruz

Şu kodu girin:

```bash
fuel-core-keygen new --key-type peering
```

Çıktı olarak `peer_id, secret ve node tipinizi` verecek. Bunları kopyalayıp güvenli bir yere kaydedin.

## Config Dosyamızı Oluşturuyoruz

`chainConfig.json` dosyamızı oluşturup düzenleyeceğiz. Öncelikle şu kodu girelim:
```bash
nano chainConfig.json
```

[chainConfig.json](./chainConfig.json) dosyasının içerisindeki her şeyi kopyalıyoruz ve terminale yapıştırıyoruz.

Ctrl + X yaptıktan sonra `y` yazıp enterlıyoruz.

Her şeyi aynen dediğim gibi yaptıysanız artık nodeunuzu başlatmaya hazırsınız demektir.

## Nodeumuzu Başlatalım
Aşağıdaki kodu çalıştırdığınız zaman nodeunuz kısa bir sürede senkronize olacaktır. **Koddaki birkaç şeyi değiştirmemiz gerekiyor.** Aşağıda bunları açıkladım.

`{NODE_NICKINIZ}` -> Kendi nodeunuza vereceğiniz isim. Örneğin ben bu kısmı değiştirip 0xemin olarak yazdım. İstediğiniz ismi verebilirsiniz.

`{GIZLI_KELIME}` -> P2P Key Üretiyoruz kısmındaki çıktıda bize secret diye bir şifre vermişti. Onu buraya yazacağız.

`{RPC}` -> Alchemy ya da Infura'dan aldığınız RPC'yi buraya yazın.

Kullanacağımız kod:

```bash
fuel-core run \
--service-name {NODE_NICKINIZ} \
--keypair {GIZLI_KELIME} \
--relayer {RPC} \
--ip 0.0.0.0 --port 4000 --peering_port 30333 \
--db-path  ~/.fuel_beta4 \
--chain ./chainConfig.json \
--utxo-validation --poa-instant false --network beta-4 --enable-p2p \
--min-gas-price 1 --max_block_size 18874368  --max_transmit_size 18874368 \
--bootstrap_nodes /dns4/p2p-beta-4.fuel.network/tcp/30333/p2p/16Uiu2HAm3xjsqASZ68KpaJPkPCMUiMgquhjyDHtxcVxVdFkMgRFf,/dns4/p2p-beta-4.fuel.network/tcp/30334/p2p/16Uiu2HAmJyoJ2HrtPRdBALMT8fs5Q25xVj57gZj5s6G6dzbHypoS \
--sync_max_get_header 100 --sync_max_get_txns 100 \
--relayer-v2-listening-contracts 0x03f2901Db5723639978deBed3aBA66d4EA03aF73 \
--relayer-da-finalization 4 \
--relayer-da-deploy-height 4111672 \
--relayer-log-page-size 2000

```
![image](https://github.com/eminmtas/fuel-beta4-node-kurma-rehberi/assets/44838743/e26bccfe-9423-424e-ad64-13f1a8e1d810)


Loglar yukarıdaki gibi akıyorsa Tebrikler, node başarılı bir şekilde kuruldu. Otomatik olarak senkron olacak. Gizli kelimelerinizi bir yere kaydetmeyi ve beni takip etmeyi unutmayın :)

[**emin.pdf**](https://x.com/0x_Emin)
