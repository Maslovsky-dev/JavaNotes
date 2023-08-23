---
tags: Паттерны
---
Паттерн "Фасад" - это структурный паттерн проектирования, который позволяет скрыть сложность внутренней системы за простым внешним интерфейсом. Он предоставляет унифицированный интерфейс к группе интерфейсов в подсистеме, чтобы упростить взаимодействие с этой подсистемой.

Давайте рассмотрим пример на Java, чтобы лучше понять паттерн "Фасад":
Предположим, у нас есть сложная система, предоставляющая различные компоненты для работы с мультимедийными файлами: аудио и видео. Внутри этой системы есть классы для кодирования, декодирования, сжатия, распаковки и других операций. Вместо того чтобы заставлять клиентский код взаимодействовать напрямую со всеми этими классами, мы можем создать фасад, который будет предоставлять более простой интерфейс для работы с мультимедийными файлами.
```java
// Внутренние классы сложной системы
class AudioEncoder {
    public void encode() {
        System.out.println("Аудио кодируется...");
    }
}

class VideoEncoder {
    public void encode() {
        System.out.println("Видео кодируется...");
    }
}

class Compression {
    public void compress() {
        System.out.println("Сжатие выполняется...");
    }
}

// Фасад
class MultimediaFacade {
    private AudioEncoder audioEncoder;
    private VideoEncoder videoEncoder;
    private Compression compression;

    public MultimediaFacade() {
        audioEncoder = new AudioEncoder();
        videoEncoder = new VideoEncoder();
        compression = new Compression();
    }

    public void convertAndCompressAudio() {
        audioEncoder.encode();
        compression.compress();
        System.out.println("Аудио готово!");
    }

    public void convertAndCompressVideo() {
        videoEncoder.encode();
        compression.compress();
        System.out.println("Видео готово!");
    }
}

// Клиентский код
public class Main {
    public static void main(String[] args) {
        MultimediaFacade facade = new MultimediaFacade();

        facade.convertAndCompressAudio();
        facade.convertAndCompressVideo();
    }
}

```
В этом примере класс MultimediaFacade выступает в роли фасада. Он предоставляет упрощенный интерфейс для выполнения операций с аудио и видео, скрывая сложность внутренних классов. Клиентский код взаимодействует только с фасадом, что делает код более читаемым и поддерживаемым.

Таким образом, паттерн "Фасад" позволяет сократить сложность системы, обеспечивая клиентам более простой и понятный интерфейс для взаимодействия с подсистемой
