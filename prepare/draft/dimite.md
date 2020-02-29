PropertyChangeSupport propertySupport

.addPropertyChangeListener(PropertyChangeListener listener);

.removePropertyChangeListener(PropertyChangeListener listener);

.firePropertyChange(String propertyName, Object oldValue, Object newValue);


new PropertyChangeListener() {
                    public void propertyChange(PropertyChangeEvent evt) {
                        System.out.println(evt);
                    }
                }


char propName[] = name.substring("set".length()).toCharArray();
propName[0] = Character.toLowerCase(propName[0]);
String propertyName = new String(propName)


