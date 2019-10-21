# Maps Realtime - Client

Projeto de testes com websocket PHP para exibir a posição em tempo real do usuário.

Esse projeto é o front-end da aplicação, sendo necessário para seu funcionamento lado servidor da aplicação, esse disposto em https://github.com/debgustavocastro/maps-realtime-server.

Essa aplicação foi construída utilizando como base o framework VueJS.

## Instalação do projeto
```
#Acessa o diretório da aplicação
cd  maps-realtime-client

#Instala as dependências
npm install
```

## Configurando o projeto
No arquivo src/components/Template.vue:61, deve-se setar o endereço do servidor que contém o socket.


### Compilar para desenvolvimento
```
npm run serve
```

### Compilar e minificar para produção
```
npm run build
```