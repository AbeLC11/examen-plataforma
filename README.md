const model = require('./modelo')

function agregarProducto( producto ) {
    const objeto = new model( producto )
    objeto.save()
}

function obtenerProductos( filtroProducto ) {
    let filtro = {}
    if (filtroProducto) {
        filtro = { codigo: filtroProducto }
    }
    const objeto = model.find( filtro )
    return objeto
}

function actualizarProducto( producto ) {
    const objeto = model.findOne( {codigo: producto.codigo} )
    objeto.serie = producto.serie
    objeto.nombre = producto.nombre
    objeto.stock = producto.stock
    objeto.valor = producto.valor
    const resultado = objeto.save()
    return resultado
}

function eliminarProducto( codigo ) {
    return model.deleteOne({codigo: codigo})
}

module.exports = {
    agregar: agregarProducto,
    obtener: obtenerProductos,
    actualizar: actualizarProducto,
    eliminar: eliminarProducto,
}



const storage = require('./almacenamiento')

function agregarProducto( producto ) {
    return new Promise((resolve, reject) => {
        storage.agregar( producto )
        resolve( producto )
    })
}

function obtenerProductos( filtroProducto ) {
    return new Promise((resolve, reject) => {
        resolve( storage.obtener( filtroProducto ) )
    })
}

function actualizarProducto( serie, nombre, stock, valor ) {
    return new Promise((resolve, reject) => {
        let producto = {
            serie: serie,
            nombre: nombre,
            stock: stock,
            valor: valor
        }
        storage.actualizar( producto )
        resolve( producto )
    })
}

function eliminarProducto( codigo ) {
    return new Promise((resolve, reject) => {
        storage.eliminar( codigo )
        resolve( codigo )
    })
}

module.exports = {
    agregarProducto,
    obtenerProductos,
    actualizarProducto,
    eliminarProducto,
}



const express = require('express')
const respuesta = require('../../red/respuestas')
const controlador = require('./controlador')

const ruta = express.Router()

ruta.get('/', function(req, res) {
    const filtroProducto = req.query.producto || null
    controlador.obtenerProductos( filtroProducto )
        .then((data) => {
            respuesta.exito(req, res, data, 200)
        })
        .catch((error) => {
            respuesta.error(req, res, error, 500)
        })
})

ruta.post('/', function(req, res) {
    controlador.agregarProducto( req.body )
        .then((data) => {
            respuesta.exito(req, res, data, 200)
        })
        .catch((error) => {
            respuesta.error(req, res, error, 500)
        })
})

ruta.patch('/', function(req, res) {
    controlador.actualizarProducto(req.body.serie, req.body.nombre, req.body.stock, req.body.valor)
        .then((data) => {
            respuesta.exito(req, res, data, 200)
        })
        .catch((error) => {
            respuesta.error(req, res, error, 500)
        })
})

ruta.delete('/', function(req, res) {
    controlador.eliminarProducto(req.body.abreviatura)
        .then((data) => {
            respuesta.exito(req, res, data, 200)
        })
        .catch((error) => {
            respuesta.error(req, res, error, 500)
        })
})

module.exports = ruta



const mongoose = require('mongoose')
const Abel = mongoose.Abel

const reqString = {
    type: String,
    required: true,
}

const reqNumber = {
    type: Number,
    required: true,
}

const proveedorAbel = new Abel({
    nombre: reqString,
    Valor: reqString,
})

const productoAbel = new Abel({
    Serie: reqNumber,
    nombre: reqString,
    stock: reqNumber,
    Valor: reqString,
})

const model = mongoose.model('Producto', productoAbel)
module.exports = model
