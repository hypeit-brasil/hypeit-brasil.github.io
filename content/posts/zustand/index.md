---
title: "Gerenciando estado no React com Zustand"
date: 2022-11-06T02:19:57-03:00
draft: tfalserue
tags: ['react', 'vite', 'gerenciamento de estado']
ShowToc: true
disableHLJS: false
TocSide: 'right'
---

Você certamente já se deparou com a necessidade de consumir um estado compartilhado entre
vários componentes no React em lugares distintos de sua aplicação e
certamente já ouviu falar sobre o Redux como uma ferramenta robusta de gerenciar o estado.

Embora muito utilizado e bem documentado, é inegável que muitas vezes sua
utilização é um bocado _**verbosa**_, necessitando de muito _**boilerplate**_
para conseguir atingir uma funcionalidade básica.

Neste artigo iremos utilizar um gerenciador de estado leve, rápido e de fácil
utilização chamado _**zustand**_ para resolver um problema de gerenciamento de estado de uma aplicação e vamos analisar suas principais características e pontos fortes.

> Para abrir o projeto completo, acesse através do [github](hypeit-brasil/zustand) ou clique no botão abaixo para abrir diretamente no _stackblitz_

[![Open in StackBlitz](https://developer.stackblitz.com/img/open_in_stackblitz.svg)](https://stackblitz.com/github/hypeit-brasil/zustand)

# Setup

Primeiramente, iremos criar uma nova aplicação react, neste tutorial
utilizaremos o **vite**.

```sh
npm create vite@latest -- --template react-ts
```

em seguida ele irá pedir por um nome e irá criar um diretório com o código fonte
do seu projeto, agora basta realizar a instalação dos módulos:

```sh
cd pasta-do-projeto
npm i zustand
```

## Instalando Chakra UI

Com o módulo do **zustand** instalado já estamos prontos para utilizar a biblioteca,
porém, irei utilizar uma biblioteca de elementos de UI para agilizar na
estilização da aplicação, o
[Chakra UI](https://chakra-ui.com/getting-started/vite-guide).

```bash
npm i @chakra-ui/react @emotion/react@^11 @emotion/styled@^11 framer-motion@^6
```

\
Modificando o arquivo: `src/main.tsx`

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import App from "./App";
import { ChakraProvider } from "@chakra-ui/react";

ReactDOM.createRoot(document.getElementById("root") as HTMLElement).render(
  <React.StrictMode>
    <ChakraProvider>
      <App />
    </ChakraProvider>
  </React.StrictMode>,
);
```

## Excluindo arquivos desnecessários

Agora, delete os arquivos:

- `src/App.css`;
- `src/index.css`;
- `src/assets/react.svg`.

e em `App.tsx` apague todo o conteúdo, deixando uma estrutura básica de
componente:

```tsx
import React from "react";

export const App = () => {
  return <div />;
};

export default App;
```

## Modificando script de inicialização

Por padrão o **vite** sobe a aplicação de desenvolvimento em alguma porta
aleatória e isso é muito chato para o desenvolvimento, portanto, no arquivo
`package.json` iremos modificar a `linha 7` para especificar uma porta padrão,
neste caso utilizaremos a porta `3333`.

```
"scripts": {
  "dev": "vite --port 3333",
  ...
},
```

# Criando a base da aplicação

Nossa aplicação de exemplo será simples, porém terá uma série de oportunidades
de demonstrar como podemos utilizar o gerenciamento de estado do **Zustand** em
ação.

A aplicação será um fragmento de um E-commerce, contendo um componente para
definir o endereço de entrega do destinatário e um outro componente para
apresentar o endereço de entrega salvo.

Eles serão:

- `src/components/AddressForm.tsx`
- `src/components/AddressSummary.tsx`

{{<details-tag `Código do AddressForm.tsx`>}}

```tsx
import {
  Button,
  FormControl,
  FormHelperText,
  FormLabel,
  Input,
  Select,
  VStack,
} from "@chakra-ui/react";
import React, { useState } from "react";

const AddressForm: React.FC = () => {
  const [uf, setUf] = useState("");
  const [address, setAddress] = useState("");
  const [complement, setComplement] = useState("");

  const handleSubmit: React.FormEventHandler<HTMLDivElement> = (event) => {
    event.preventDefault();
    alert(JSON.stringify({
      uf,
      address,
      complement,
    }));
  };

  return (
    <VStack as={"form"} onSubmit={handleSubmit}>
      <FormControl>
        <FormLabel>Estado</FormLabel>
        <Select
          value={uf}
          onChange={(e) => setUf(e.target.value)}
          bg={"white"}
          placeholder="Selecione sua Unidade Federativa"
        >
          <option value="AC">Acre</option>
          <option value="AL">Alagoas</option>
          <option value="AP">Amapá</option>
          <option value="AM">Amazonas</option>
          <option value="BA">Bahia</option>
          <option value="CE">Ceará</option>
          <option value="DF">Distrito Federal</option>
          <option value="ES">Espírito Santo</option>
          <option value="GO">Goiás</option>
          <option value="MA">Maranhão</option>
          <option value="MT">Mato Grosso</option>
          <option value="MS">Mato Grosso do Sul</option>
          <option value="MG">Minas Gerais</option>
          <option value="PA">Pará</option>
          <option value="PB">Paraíba</option>
          <option value="PR">Paraná</option>
          <option value="PE">Pernambuco</option>
          <option value="PI">Piauí</option>
          <option value="RJ">Rio de Janeiro</option>
          <option value="RN">Rio Grande do Norte</option>
          <option value="RS">Rio Grande do Sul</option>
          <option value="RO">Rondônia</option>
          <option value="RR">Roraima</option>
          <option value="SC">Santa Catarina</option>
          <option value="SP">São Paulo</option>
          <option value="SE">Sergipe</option>
          <option value="TO">Tocantins</option>
        </Select>
      </FormControl>
      <FormControl>
        <FormLabel>Seu Endereço</FormLabel>
        <Input
          value={address}
          onChange={(e) => setAddress(e.target.value)}
          bg={"white"}
          type="text"
        />
        <FormHelperText>Insira a rua e o número (se existente).</FormHelperText>
      </FormControl>
      <FormControl>
        <FormLabel>Complemento (opcional)</FormLabel>
        <Input
          value={complement}
          onChange={(e) => setComplement(e.target.value)}
          bg={"white"}
          type="text"
        />
      </FormControl>
      <Button
        disabled={address.length === 0 || uf.length === 0}
        alignSelf={"end"}
        colorScheme={"blue"}
        type="submit"
      >
        Salvar
      </Button>
    </VStack>
  );
};

export default AddressForm;
```

{{</details-tag>}}

{{<details-tag `Código do AddressSummary.tsx`>}}

```tsx
import { Box, Text, VStack } from "@chakra-ui/react";
import React from "react";

const AddressSummary: React.FC = () => {
  return (
    <VStack spacing={"4"} alignItems={"start"}>
      <Text alignSelf={"center"}>
        Revise as informações abaixo antes de confirmar a compra
      </Text>
      <Box>
        <Text fontWeight={"bold"}>Estado</Text>
        <Text fontSize={"lg"}>SP</Text>
      </Box>
      <Box>
        <Text fontWeight={"bold"}>Endereço</Text>
        <Text fontSize={"lg"}>Rua dos passarinhos, 89</Text>
      </Box>
      <Box>
        <Text fontWeight={"bold"}>Complemento</Text>
        <Text fontSize={"lg"}>apto 104</Text>
      </Box>
    </VStack>
  );
};

export default AddressSummary;
```

{{</details-tag>}}

\
`src/App.tsx` contendo os dois componentes:

```tsx
import { Box, Container } from "@chakra-ui/react";
import AddressForm from "./components/AddressForm";
import AddressSummary from "./components/AddressSummary";

export const App = () => {
  return (
    <Container maxW={"container.lg"} py={"10"}>
      <Box p={4} rounded="md" shadow={"base"} bg={"gray.50"}>
        <AddressForm />
      </Box>
      <Box mt={4} p={4} rounded="md" shadow={"base"} bg={"gray.50"}>
        <AddressSummary />
      </Box>
    </Container>
  );
};

export default App;
```

Como resultado das implementações teremos a seguinte UI:

![Aplicação base](images/app-initial.gif 'Aplicação base')

# Criando nosso _store_ global

Toda vez que criamos um estado no **Zustand**, por padrão este estado criado é
global, ou seja, ele pode ser utilizado por toda a aplicação.

O que é mais surpreendente, é que não é necessário encapsular nossos componentes
dentro de um _provider_ ou algo do tipo, basta somente consumir este estado
global e pronto!

Vamos agora criar este nosso estado global de _**Address**_:

1. Primeiro, vamos definir a interface desse estado, nesta interface iremos
   definir quais são as propriedades e métodos deste objeto, as propriedades nos
   dirão quais os dados que podemos consumir e os métodos serão os responsáveis
   pela mutação destes dados.

`src/store/AddressStore.ts`

{{< highlight ts "linenos=table,linenostart=1" >}}
interface Address {
  uf: string;
  address: string;
  complement?: string;
}

export interface IAddressStore {
  address: Address | null;
  setAddress: (address: Address) => void;
}
{{< / highlight >}}

2. Agora, com a interface definida, vamos criar o objeto que representará nosso estado global, o _store_, que exportaremos no formato de um _**hook**_:

{{< highlight ts "linenos=table,linenostart=1" >}}
import create from "zustand";

// Código das interfaces (passo 1)

export const useAddressStore = create<IAddressStore>(set => ({
  address: null,
  setAddress: (address) =>
    set({
      address,
    }),
}));
{{< / highlight >}}

## Explicando o código acima

A função create, importada da biblioteca **zustand** é resposável por criar o *store* em si, ela recebe um tipo genérico que define a estrutura do *store*, indispensável para obtermos o *intellisense* no editor de texto, no meu caso, o _VSCode_.

Esta função recebe uma função anônima como argumento, que é composta por dois parâmetros: `set` e `get`, e retorna um objeto de resposta que será a estrutura do nosso store, o seu tipo é definido pela interface que passamos como tipo genérico na chamada da função create.

O parâmetro `set` é utilizado para definir as mutações dentro do nosso *store*, sempre que nós quisermos alterar alguma propriedade do nosso store, utilizaremos o parâmetro `set` para  fazê-lo, este parâmetro também é uma função, e esta função recebe como parâmetro um objeto que satisfaça a estrutura do nosso store, como por exemplo, um objeto do tipo `Address`.

O parâmetro `get` é utilizado para recuperar uma propriedade do nosso *store*.


# Conectando o _store_ aos componentes

Agora o próximo passo é utilizar o estado que criamos nos nossos componentes, primeiramente vamos conectar o componente `AddressSumary.tsx` ao store utilizando o **hook** que criamos.


{{< highlight ts "linenos=table,linenostart=1" >}}
import { Box, VStack, Text, Alert, AlertDescription, AlertIcon, AlertTitle } from '@chakra-ui/react';
import React from 'react';
import { useAddressStore } from '../store/AddressStore';

const AddressSummary: React.FC = () => {

  const address = useAddressStore(state => state.address);

  return <VStack spacing={'4'} alignItems={'start'}>
    <Text alignSelf={'center'}>Revise as informações abaixo antes de confirmar a compra</Text>
    {
      address ?
        <>
          <Box>
            <Text fontWeight={'bold'}>Estado</Text>
            <Text fontSize={'lg'}>{address.uf}</Text>
          </Box>
          <Box>
            <Text fontWeight={'bold'}>Endereço</Text>
            <Text fontSize={'lg'}>{address.address}</Text>
          </Box>
          <Box>
            <Text fontWeight={'bold'}>Complemento</Text>
            <Text fontSize={'lg'}>{address.complement || 'Sem complemento'}</Text>
          </Box>
        </>
        :
        <Alert rounded={'md'} status='error'>
          <AlertIcon />
          <AlertTitle>Nenhum endereço cadastrado</AlertTitle>
        </Alert>
    }
  </VStack>;
}

export default AddressSummary;
{{< / highlight >}}

Temos o a página de endereço conectada ao nosso _store_, como nosso _store_ é inicializado com a propriedade `address: null`, neste primeiro momento teremos somente o componente de `Alert`:


![Estado vazio](images/primeira-conexao-estado.png 'Estado vazio')

## Salvando os dados do formulário no store

Agora, precisamos conectar o componente de formulário no _store_, tendo os seguintes comportamentos:
1. No carregamento inicial do componente, os dados iniciais deverão ser carregados com o que está presente no _store_;
2. Ao clicar em salvar, os dados presentes no formulário deverão ser salvos no _store_.

### implementando o primeiro comportamento:

`src/components/AddressForm.tsx`
{{< highlight tsx "linenos=table,linenostart=11" >}}
  const addressStore = useAddressStore(store => store.address);

  useEffect(() => {
    if (addressStore) {
      setUf(addressStore.uf);
      setAddress(addressStore.address);
      setComplement(addressStore.complement ?? '');
    }
  }, [addressStore]);
{{< / highlight >}}

Como podemos observar, estamos nos inscrevendo no _store_ de _address_, especificamente na propriedade `address` e sincronizando com o nosso state da aplicação através do `useEffect`, desta forma, quando houver alguma mudança na propriedade `address` e **somente** na propriedade `address` do nosso store, os dados serão sincronizados (ocasionando uma renderização do componente).

### Implementando o segundo comportamento

{{< highlight tsx "linenos=table,linenostart=19" >}}
  const setAddressStore = useAddressStore(store => store.setAddress);

  const handleSubmit: React.FormEventHandler<HTMLDivElement> = (event) => {
    event.preventDefault();
    setAddressStore({
      uf,
      address,
      complement
    })
  };
{{< / highlight >}}

De forma semelhante à que fizemos na implementação do primeiro comportamento, agora, selecionamos a porção do store que possui a função setAddres e a chamamos na função `handleSubmit`, responsável por lidar com o evento de envio do formulário de endereço.

Agora a nossa aplicação está com o comportamento esperado:

![Store conectado](images/app-store-conectado.gif 'Store conectado')

# Persistindo o estado da aplicação no _LocalStorage_

Há porém um problema na nossa implementação, ao atualizar a página o estado da aplicação (que é mantido em memória) é apagado, nos levando a ter que preencher novamente o endereço, por sorte, a biblioteca **Zustand** possui os _**Middlewares**_, que são funcionalidades extras que podemos anexar ao nosso store criado, e um dos middlewares que vêm junto com a biblioteca é o Middleware de persistência, que iremos implementar agora.


`src/store/AddressStore.ts`
{{< highlight ts "linenos=table,linenostart=1" >}}
import create from "zustand";
import { persist } from "zustand/middleware";

// Codigo das interfaces

export const useAddressStore = create(
  persist<IAddressStore>(set => ({
    address: null,
    setAddress: (address) =>
      set({
        address,
      }),
  }),
    {
      name: 'address-store'
    }
  )
);
{{< / highlight >}}

Para implementar o middleware de persistência é muito simples, basta importar o `persist` de dentro do módulo `zustand/middleware` e colocar tudo que ficava dentro do `create` para dentro da chamada desta função `persist`, colocar o tipo genérico que ficava em `create` em `persist` e enfim, adicionar um segundo parâmetro dentro da chamada à função `persist` que será a configuração deste middleware de persistência, neste caso colocarei apenas uma chave chamada name, identificando no `localstorage` que se trata do estado guardado correspondente ao `store` de `address`.

# Conclusão

Ainda tem bastante coisa para discutir dentro do tópico de zustand, contudo com o que vimos, é realmente muito simples implementar o `zustand` dentro das nossas aplicações React para obter um gerenciamento de estado global com poucas linhas de código.

Sem dúvidas, ainda tem bastante coisa que podemos discutir e explorar dentro dessa biblioteca fenomenal, podem esperar mais conteúdo sobre ela.
Abraços!!