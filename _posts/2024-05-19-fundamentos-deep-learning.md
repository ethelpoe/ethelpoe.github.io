---
title: "Fundamentos de Deep Learning con PyTorch"
date: 2024-05-17T00:00:00-04:00
categories: [Tutorial, Deep Learning]
tags: [pytorch, neural-networks, deep-learning, transfer-learning]
---

# Deep Learning Básico con PyTorch

## ¿Por qué Deep Learning?

El Deep Learning es una rama del Machine Learning que utiliza redes neuronales profundas para aprender representaciones jerárquicas de datos. Es especialmente poderoso en:
- Procesamiento de imágenes
- Procesamiento de lenguaje natural
- Reconocimiento de voz
- Generación de contenido

### ¿Por qué PyTorch?
- Sintaxis más pythónica y clara
- Debugging más sencillo (modo eager por defecto)
- Popular en investigación
- Gran comunidad y recursos
- Flexible y extensible

## Configuración del Entorno

```python
import torch
import torch.nn as nn
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms
import matplotlib.pyplot as plt
import numpy as np

# Verificar disponibilidad de GPU
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
print(f"Usando dispositivo: {device}")
```

## Conceptos Fundamentales

### 1. Tensores
Los tensores son la estructura de datos fundamental en PyTorch:

```python
# Crear tensores
x = torch.tensor([[1, 2, 3], [4, 5, 6]])
y = torch.zeros(2, 3)
z = torch.randn(2, 3)  # Distribución normal

# Operaciones básicas
suma = x + y
producto = torch.matmul(x, z.T)
exponencial = torch.exp(x)

# Mover a GPU si está disponible
x_gpu = x.to(device)
```

### 2. Autograd (Diferenciación Automática)
PyTorch calcula gradientes automáticamente:

```python
# Crear tensor con gradientes
x = torch.randn(3, requires_grad=True)
y = x * 2
z = y.mean()

# Calcular gradientes
z.backward()
print(f"Gradientes de x: {x.grad}")
```

## Redes Neuronales Básicas

### 1. Red Neuronal Feedforward
```python
class RedNeuronal(nn.Module):
    def __init__(self):
        super(RedNeuronal, self).__init__()
        self.flatten = nn.Flatten()
        self.capas = nn.Sequential(
            nn.Linear(784, 128),
            nn.ReLU(),
            nn.Dropout(0.2),
            nn.Linear(128, 64),
            nn.ReLU(),
            nn.Dropout(0.2),
            nn.Linear(64, 10)
        )

    def forward(self, x):
        x = self.flatten(x)
        return self.capas(x)

# Crear modelo
modelo = RedNeuronal().to(device)
```

### 2. Entrenamiento Básico
```python
def entrenar_modelo(modelo, train_loader, criterion, optimizer, epochs):
    modelo.train()
    for epoch in range(epochs):
        perdida_total = 0
        aciertos = 0
        total = 0
        
        for batch_idx, (datos, etiquetas) in enumerate(train_loader):
            datos, etiquetas = datos.to(device), etiquetas.to(device)
            
            # Forward pass
            salidas = modelo(datos)
            perdida = criterion(salidas, etiquetas)
            
            # Backward pass y optimización
            optimizer.zero_grad()
            perdida.backward()
            optimizer.step()
            
            # Estadísticas
            perdida_total += perdida.item()
            _, predicciones = torch.max(salidas.data, 1)
            total += etiquetas.size(0)
            aciertos += (predicciones == etiquetas).sum().item()
            
            if batch_idx % 100 == 0:
                print(f'Epoch [{epoch+1}/{epochs}] Batch [{batch_idx}/{len(train_loader)}] '
                      f'Pérdida: {perdida.item():.4f} '
                      f'Precisión: {100 * aciertos/total:.2f}%')
```

## Ejemplo Práctico: Clasificación de Imágenes MNIST

```python
# Preparar datos
transform = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize((0.1307,), (0.3081,))
])

# Cargar MNIST
train_dataset = torchvision.datasets.MNIST(
    root='./data', 
    train=True,
    download=True,
    transform=transform
)

train_loader = torch.utils.data.DataLoader(
    train_dataset,
    batch_size=64,
    shuffle=True
)

# Configurar modelo y entrenamiento
modelo = RedNeuronal().to(device)
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(modelo.parameters(), lr=0.001)

# Entrenar
entrenar_modelo(modelo, train_loader, criterion, optimizer, epochs=5)
```

## Transfer Learning

El transfer learning permite aprovechar modelos pre-entrenados:

```python
import torchvision.models as models

class ModeloTransfer(nn.Module):
    def __init__(self, num_clases):
        super(ModeloTransfer, self).__init__()
        # Cargar ResNet pre-entrenado
        self.resnet = models.resnet18(pretrained=True)
        
        # Congelar parámetros
        for param in self.resnet.parameters():
            param.requires_grad = False
            
        # Modificar última capa
        num_features = self.resnet.fc.in_features
        self.resnet.fc = nn.Linear(num_features, num_clases)
    
    def forward(self, x):
        return self.resnet(x)

# Crear modelo de transfer learning
modelo_transfer = ModeloTransfer(num_clases=10).to(device)
```

## Visualización y Monitoreo

### 1. Visualizar Pérdida y Precisión
```python
def plot_metricas(historia):
    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 4))
    
    ax1.plot(historia['perdida'])
    ax1.set_title('Pérdida durante entrenamiento')
    ax1.set_xlabel('Epoch')
    ax1.set_ylabel('Pérdida')
    
    ax2.plot(historia['precision'])
    ax2.set_title('Precisión durante entrenamiento')
    ax2.set_xlabel('Epoch')
    ax2.set_ylabel('Precisión')
    
    plt.tight_layout()
    plt.show()
```

### 2. Visualizar Predicciones
```python
def visualizar_predicciones(modelo, test_loader, num_imagenes=5):
    modelo.eval()
    fig = plt.figure(figsize=(12, 4))
    
    with torch.no_grad():
        for i, (imagenes, etiquetas) in enumerate(test_loader):
            if i >= num_imagenes:
                break
                
            ax = fig.add_subplot(1, num_imagenes, i+1)
            imagen = imagenes[0].numpy().transpose((1, 2, 0))
            ax.imshow(imagen)
            
            salida = modelo(imagenes.to(device))
            _, prediccion = torch.max(salida, 1)
            
            ax.set_title(f'Pred: {prediccion.item()}\nReal: {etiquetas[0]}')
            ax.axis('off')
    
    plt.tight_layout()
    plt.show()
```

## Ejercicios Prácticos

1. **Básico**: Red Neuronal Simple
   - Implementar una red para clasificación binaria
   - Experimentar con diferentes arquitecturas
   - Visualizar el proceso de entrenamiento

2. **Intermedio**: Clasificación de Imágenes
   - Usar el dataset CIFAR-10
   - Implementar una CNN personalizada
   - Aplicar data augmentation

3. **Avanzado**: Transfer Learning
   - Fine-tuning de ResNet en un dataset propio
   - Implementar early stopping
   - Usar técnicas de regularización

## Recursos Adicionales

### Documentación Oficial
- [PyTorch Tutorials](https://pytorch.org/tutorials/)
- [PyTorch Documentation](https://pytorch.org/docs/stable/index.html)
- [torchvision Reference](https://pytorch.org/vision/stable/index.html)

### Tutoriales Recomendados
- [PyTorch for Deep Learning](https://www.fast.ai)
- [Deep Learning with PyTorch](https://pytorch.org/deep-learning-with-pytorch)
- [Dive into Deep Learning](https://d2l.ai/)

## Próximos Pasos
1. Explorar arquitecturas más avanzadas
2. Practicar con diferentes datasets
3. Participar en competencias de Kaggle
4. Comenzar con proyectos específicos (NLP/Computer Vision)

---
*Este post es parte del [Programa Intensivo de Especialización en IA Aplicada](/2024-05-19-syllabus-ia-aplicada). El siguiente post cubrirá proyectos prácticos de ML/DL.*
