# Projeto NoticiarioAPI com Testes Unitários e de Integração + App Insights
#
## Descrição
Nesta API é possível efetuar um CRUD tanto para Usuários quanto para as Noticias, e foram adicionados testes para validar estes cenários.
Utilizando testes unitários e também o teste de integração.
#
# Testes Unitários
### Objetivo.
Validar cada função isoladamente dentro da aplicação.
OBS: Foram utilizadas as bibliotecas Moq e XUnit para efetuar os testes de Unidade, e também foram criadas Listas de dados Fake para evitar o uso do banco de dados.
#
# Validando UsuarioController.
### Funções de Validação do UsuarioController
+ GetMock_ListaUsers_Sucesso_ReturnsStatusCode200()
+ PostMock_Add_Sucesso_Returns()
+ Get_PegaPorId_Sucesso_Returns()
+ Put_Atualiza_Sucesso_Returns()
+ Delete_Deleta_User_Sucesso_Returns()
#
### Configurações utilizadas para criar o objeto Mock e efetuar o Arrange.
+ *Configurando o objeto Mock para receber os parâmetro necessários para efetuar uma consulta e retornar uma lista de objetos.*
```C#
_uof.Setup(u => u.UsuarioRepository.Lista(It.IsAny<int>(), It.IsAny<int>()))
                  .Returns<int, int>((skip, take) => usersFake.Skip(skip).Take(take).AsQueryable());
```
+ *Configurando o objeto Mock para receber uma expressão delegate que vai validar a consulta do objeto na lista userFake, e retornar um objeto de userFake.*
```C#
_uof.Setup(u => u.UsuarioRepository.PegaPorID(It.IsAny<Expression<Func<Usuario, bool>>>()))
                    .ReturnsAsync((Expression<Func<Usuario, bool>> predicate) =>
                    {
                        return usersFake.Single(predicate.Compile());
                    });
```
+ *Configurando o Objeto Mock para receber um tipo Usuario e efetuar sua adição na lista userFake caso não encontre outro usuário de mesmo ID*
```C#
_uof.Setup(repo => repo.UsuarioRepository.Add(It.IsAny<Usuario>()))
                .Callback((Usuario user) =>
                {
                    if (!usersFake.Any(u => u.Id == user.Id)) usersFake.Add(user);
                });
```
+ *Objeto Mock configurado para receber um tipo Usuario, verificar se seu ID existe na lista userFake, caso sim, ele atualiza o objeto retornado com os novos valores do parâmetro Usuário*
```C#
_uof.Setup(repo => repo.UsuarioRepository.Atualiza(It.IsAny<Usuario>()))
                .Callback((Usuario user) =>
                {
                    var consulta = usersFake.FirstOrDefault(x => x.Id == user.Id);
                    if (consulta != null)
                    {
                        consulta.Name = user.Name;
                        consulta.Email = user.Email;
                        consulta.Password = user.Password;
                        consulta.Role = user.Role;
                    }
                });
```
+ *Objeto Mock configurado para receber um tipo Usuario, verificar se seu ID existe na lista userFake, caso sim, ele remove o objeto da lista userFake*
```C#
_uof.Setup(repo => repo.UsuarioRepository.Deleta(It.IsAny<Usuario>()))
                  .Callback((Usuario user) =>
                  {
                      var consulta = usersFake.FirstOrDefault(u => u.Id == user.Id);
                      if (consulta != null)
                      {
                          usersFake.Remove(consulta);
                      }
                  });
```
#
# Validando o NoticiaController
### Funções de Validação do NoticiaController
+ Get_ListaNoticias_Sucesso_Returns()
+ Get_PegaPorId_Sucesso_Returns()
+ Post_Add_Noticia_Sucesso_Returns()
+ Atualiza_Noticia_Sucesso_Returns()
+ Deleta_Noticia_Sucesso_Returns()
#
### Configurações utilizadas para criar o objeto Mock e efetuar o Arrange.
+ *Configurando o objeto Mock para receber os parâmetro necessários para efetuar uma consulta e retornar uma lista de objetos.*
```C#
_uof.Setup(repo => repo.NoticiaRepository.Lista(It.IsAny<int>(), It.IsAny<int>()))
                .Returns<int, int>((skip, take) => fakeNews.Skip(skip).Take(take).AsQueryable());
```
+ *Configurando o objeto Mock para receber uma expressão delegate que vai validar a consulta do objeto na lista fakeNews, e retornar um objeto de fakeNews.*
```C#
__uof.Setup(u => u.NoticiaRepository.PegaPorID(It.IsAny<Expression<Func<Noticia, bool>>>()))
                    .ReturnsAsync((Expression<Func<Noticia, bool>> predicate) =>
                    {
                        return fakeNews.Single(predicate.Compile());
                    });
```
+ *Configurando o Objeto Mock para receber um tipo Noticia e efetuar sua adição na lista fakeNews, caso não encontre outra noticia de mesmo ID*
```C#
_uof.Setup(repo => repo.NoticiaRepository.Add(It.IsAny<Noticia>()))
                    .Callback((Noticia noticia) => 
                    {
                        if(!fakeNews.Any(n => n.Id == noticia.Id)) fakeNews.Add(noticia);
                    });
```
+ *Objeto Mock configurado para receber um tipo Noticia, verificar se seu ID existe na lista fakeNews, caso sim, ele atualiza o objeto retornado com os novos valores do parâmetro Noticia*
```C#
 _uof.Setup(repo => repo.NoticiaRepository.Atualiza(It.IsAny<Noticia>()))
                .Callback((Noticia noticia) => 
                {
                    var consulta = fakeNews.FirstOrDefault(n => n.Id == noticia.Id);
                    if (consulta != null) 
                    {
                        consulta.Titulo = noticia.Titulo;
                        consulta.Autor = noticia.Autor;
                        consulta.DataPublicacao = noticia.DataPublicacao;
                        consulta.Descricao = noticia.Descricao;
                        consulta.Conteudo = noticia.Conteudo;
                    }
                });
```
+ *Objeto Mock configurado para receber um tipo Noticia, verificar se seu ID existe na lista fakeNews, caso sim, ele remove o objeto da lista fakeNews*
```C#
_uof.Setup(repo => repo.NoticiaRepository.Deleta(It.IsAny<Noticia>()))
                    .Callback((Noticia noticia) => 
                    {
                        var consulta = fakeNews.FirstOrDefault(n => n.Id == noticia.Id);
                        if(consulta != null) fakeNews.Remove(consulta);
                    });
```
#
# Validando LoginController
### Funções de Validação para LoginController
+ Autentica_Usuario_Sucesso_Returns()
#
### Configuração Utilizada.
+ *Objeto Mock configurado para receber dois parâmetros do tipo string para efetuar a consulta na lista _usuarios, simulando a autenticação, nesse caso o resultado do tipo Token não pode ser nulo.*
```C#
_uof.Setup(repo => repo.UsuarioRepository.AutenticaUser(It.IsAny<string>(), It.IsAny<string>()))
                .Callback(() => _usuarios.FirstOrDefault());
```
#
# Testes de Integração
### Objetivo.
Testar a integração entre as Unidades.
### Descrição.
Para o teste de integração foi utilizado o Postman e também o Newman, com o Postman é possível gerar coleções com as Requests necessárias, e também pre-request scripts para utilizar o input de dados de maneira randomica, junto aos tests feitos em JavaScript para validar o retorno de cada requisição. O Newman facilita no momento de rodar várias requests ao mesmo tempo e também na geração de relatórios para visualizar os resultados.
