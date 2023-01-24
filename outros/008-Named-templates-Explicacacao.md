Named Templates são templates no Helm que possuem nomes específicos e podem ser reutilizados em diferentes arquivos de template. Eles permitem que partes comuns de um template sejam definidas em um único lugar e depois chamadas em vários outros lugares.

Por exemplo, se você tem uma seção de configuração que é comum entre vários charts diferentes, você pode definir essa seção como um Named Template e, em seguida, chamá-la em cada chart onde ela é necessária.

A sintaxe para definir um Named Template é a seguinte:

~~~~YAML
{{- define "nome_do_template" -}}
Conteúdo do template aqui
{{- end -}}
~~~~

A sintaxe para chamar um Named Template é a seguinte:

~~~~YAML
{{ template "nome_do_template" . }}
~~~~

Espero que isso ajude a entender como os Named Templates funcionam no Helm.