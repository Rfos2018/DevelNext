    {
        
        
        // Generated
        $e = $event ?: $e; // legacy code from 16 rc-2
        
        $e->sender->enabled = false;
        Element::setText($this->label7, '[JiaHack] Включение анонимности.');
        waitAsync(1000, function () use ($e, $event) {
            Element::setText($this->label7, '[JiaHack] Включение анонимности..');
            waitAsync(1000, function () use ($e, $event) {
                Element::setText($this->label7, '[JiaHack] Включение анонимности...');
                waitAsync(1000, function () use ($e, $event) {
                    Element::setText($this->label7, '[JiaHack] Включение анонимности....');
                    waitAsync(1000, function () use ($e, $event) {
                        Element::setText($this->label7, 'Зашифровка SessionID');
                        waitAsync(1000, function () use ($e, $event) {
                            Element::setText($this->label7, 'Зашифровка PHPSESSID');
                            waitAsync(1000, function () use ($e, $event) {
                                Element::setText($this->label7, 'Зашифровка h');
                                waitAsync(1000, function () use ($e, $event) {
                                    Element::setText($this->label7, 'Зашифровка Name');
                                    waitAsync(1000, function () use ($e, $event) {
                                        Element::setText($this->label7, 'Начисление вимеров');
                                        waitAsync(1000, function () use ($e, $event) {
                                            Element::setText($this->label7, 'Успешно');
                                        });
                                    });
                                });
                            });
                        });
                    });
                });
            });
        });
    }

}
